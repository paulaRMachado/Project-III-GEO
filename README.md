![](https://homebay.com/wp-content/uploads/2023/03/13d96670-27b2-11ed-b52d-a3f33977cd87-Shutterstock1324591286-1024x576.jpg)
# Project III - GEOmover

## Introduction

This project covers the following scenario:

As a data engineer your goal is to place the new company offices in the best area for the company to grow. You have to find a place that more or less covers the following requirements:

- There must be some nearby companies that also do design.
- 30% of the company staff have at least 1 child.
- It should be near successful tech startups that have raised at least US$1M.
- Executives like Starbucks A LOT.
- Account managers need to travel a lot.
- Everyone in the company is between 25 and 40, give them some place to go party.
- The CEO is vegan.
- If you want to make the maintenance guy happy, a basketball stadium must be around 10 Km.
- The office dog—"Dobby" needs a hairdresser every month. Ensure there's one not too far away.

## Description
### General search :compass:
The selected approach to this project was **OPTION A**, that consists in acquiring the office of an existing company in the database.

For business to thrive it is important to stay close and connected to design companies and startups so there could be potential exchange.
Search in Mongo for these locations were:

```python
#DESIGN companies
query = {"category_code":"design"}
projection = {"name":1, "_id":0, "offices.city":1}
desing = list(c.find(query, projection))
```
The number of matches were:
- San Francisco    1
- Collingwood      1
- Berlin           1
- Ellensburg       1
- Brooklyn         1
- London           1


```python
#GAMING companies - competition/exchange
condition1 = {"category_code":"games_video"}
condition2 = {"funding_rounds.raised_amount":{"$gt": 1000000}}
query = {"$and":[condition1,condition2]}
projection = {"name":1, "_id":0, "offices.city":1}
```
Top 3 gaming cities:
- San Francisco has 39 companies;
- New York has 30;
- Los Angeles has 11.

```python
#WEB related companies 
condition1 = {"category_code":"web"}
condition2 = {"funding_rounds.raised_amount":{"$gt": 1000000}}
query = {"$and":[condition1,condition2]}
projection = {"name":1, "_id":0, "offices.city":1}
```
Top 3 web cities:
- San Francisco has 71 companies;
- New York has 65;
- Palo Alto 22.

Analizing how the list of cities with design companies intersects with Gaming and Web companies, the most appropriate city for the office would be **SAN FRANCISCO**, even though there is a lot of **competiton**, there is also potential **talent available in the city**.

### Search for an specific area within SAN FRANCISCO
In order to stay close to successfull startups that raised over US$1M, let's check out where these companies are located and how close they are to the DESIGN company.

![](image/Desired_area.jpg)

![Location of the most valued startups](image/most_valued_startups.html)

Marked with a circle is the desired area. Let's now see wich offices could make good matches to the new company and its 87 employees.

A quick query through the database shows the number of offices with adequate size:
``` python
condition1 = {"number_of_employees":{"$lt": 120}}
condition2 = {"number_of_employees":{"$gte": 90}}
condition3 = {"offices.city":"San Francisco"}
query = {"$and":[condition1,condition2,condition3]}

pipeline = [
    {"$match": query},
    {"$unwind": "$offices"},
    {"$match": {"offices.city": "San Francisco"}},
    {"$project": {"_id": 0, "name": 1, "latitude": "$offices.latitude", "longitude": "$offices.longitude", "address": "$offices.address1"}}
]
```
Let's check in the map:
![](image/possible_offices.jpg)

![Possible Offices Map](image/possible_offices.html)

### Narrowing down the options
Now let's check the other demands from the staff.
1. Starbucks
2. Schools
3. Bars
4. Vegan places for eating 
5. Grooming places for pets

![](image/most_relevant_area_heatmap.jpg)

![Condensed Heatmaps](image/heatmap_CONDENSED_venues.html)

```html
<!DOCTYPE html>
<html>
<head>
    
    <meta http-equiv="content-type" content="text/html; charset=UTF-8" />
    
        <script>
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        </script>
    
    <style>html, body {width: 100%;height: 100%;margin: 0;padding: 0;}</style>
    <style>#map {position:absolute;top:0;bottom:0;right:0;left:0;}</style>
    <script src="https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js"></script>
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css"/>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css"/>
    <link rel="stylesheet" href="https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css"/>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css"/>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css"/>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css"/>
    
            <meta name="viewport" content="width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
            <style>
                #map_22f027bd92e556e9840de6a9a2c8de6d {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            </style>
        
</head>
<body>
    
    
            <div class="folium-map" id="map_22f027bd92e556e9840de6a9a2c8de6d" ></div>
        
</body>
<script>
    
    
            var map_22f027bd92e556e9840de6a9a2c8de6d = L.map(
                "map_22f027bd92e556e9840de6a9a2c8de6d",
                {
                    center: [37.764726, -122.394523],
                    crs: L.CRS.EPSG3857,
                    zoom: 13,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );
            L.control.scale().addTo(map_22f027bd92e556e9840de6a9a2c8de6d);

            

        
    
            var tile_layer_0e7d9fe652b84c5cdeac8120fdf24f99 = L.tileLayer(
                "https://cartodb-basemaps-{s}.global.ssl.fastly.net/light_all/{z}/{x}/{y}.png",
                {"attribution": "\u0026copy; \u003ca target=\"_blank\" href=\"http://www.openstreetmap.org/copyright\"\u003eOpenStreetMap\u003c/a\u003e contributors \u0026copy; \u003ca target=\"_blank\" href=\"http://cartodb.com/attributions\"\u003eCartoDB\u003c/a\u003e, CartoDB \u003ca target=\"_blank\" href =\"http://cartodb.com/attributions\"\u003eattributions\u003c/a\u003e", "detectRetina": false, "maxNativeZoom": 18, "maxZoom": 18, "minZoom": 0, "noWrap": false, "opacity": 1, "subdomains": "abc", "tms": false}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var marker_6e2b1d8244d61603018f5b8003f5a93b = L.marker(
                [37.764726, -122.394523],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c8581028c9a03482ef749993ff1830fe = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_6e2b1d8244d61603018f5b8003f5a93b.setIcon(icon_c8581028c9a03482ef749993ff1830fe);
        
    
            marker_6e2b1d8244d61603018f5b8003f5a93b.bindTooltip(
                `<div>
                     STARTUPS: Digg
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_69d7f7bcf3ee4a64ca99d6b29ed6ca36 = L.marker(
                [37.7768052, -122.4169244],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2ba5a7af764d54cc7f3d74f78009c564 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_69d7f7bcf3ee4a64ca99d6b29ed6ca36.setIcon(icon_2ba5a7af764d54cc7f3d74f78009c564);
        
    
            marker_69d7f7bcf3ee4a64ca99d6b29ed6ca36.bindTooltip(
                `<div>
                     STARTUPS: Twitter
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_274a7286a388dc8a18dbc437062bd757 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_73ea064b50a1f9bff9a841d6103e3b10 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_274a7286a388dc8a18dbc437062bd757.setIcon(icon_73ea064b50a1f9bff9a841d6103e3b10);
        
    
            marker_274a7286a388dc8a18dbc437062bd757.bindTooltip(
                `<div>
                     STARTUPS: StumbleUpon
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_43ff728790d545b5fcab2dfdde86d374 = L.marker(
                [37.789634, -122.404052],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f715b312cc69d1cfb1433c3a806bec9f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_43ff728790d545b5fcab2dfdde86d374.setIcon(icon_f715b312cc69d1cfb1433c3a806bec9f);
        
    
            marker_43ff728790d545b5fcab2dfdde86d374.bindTooltip(
                `<div>
                     STARTUPS: Scribd
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d637f47bc791359b0368ee969942af0c = L.marker(
                [37.778613, -122.395289],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_16f74a2629baa308d3046074a9aaf0b9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d637f47bc791359b0368ee969942af0c.setIcon(icon_16f74a2629baa308d3046074a9aaf0b9);
        
    
            marker_d637f47bc791359b0368ee969942af0c.bindTooltip(
                `<div>
                     STARTUPS: Powerset
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a50dfb3dc5d7b5a9ea55dfdfa5913335 = L.marker(
                [37.779558, -122.393041],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_60b776e62744291b7475eaece02111b5 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a50dfb3dc5d7b5a9ea55dfdfa5913335.setIcon(icon_60b776e62744291b7475eaece02111b5);
        
    
            marker_a50dfb3dc5d7b5a9ea55dfdfa5913335.bindTooltip(
                `<div>
                     STARTUPS: Technorati
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5a4a87ce6c06d8e036393347fa97b20e = L.marker(
                [37.788482, -122.409173],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_1825405bb2cb7595dbe3795687c69bc3 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5a4a87ce6c06d8e036393347fa97b20e.setIcon(icon_1825405bb2cb7595dbe3795687c69bc3);
        
    
            marker_5a4a87ce6c06d8e036393347fa97b20e.bindTooltip(
                `<div>
                     STARTUPS: Kyte
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c8774f1ef75f8fd541a0cb2bc4e0a739 = L.marker(
                [37.793148, -122.402567],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_abb4659e99eb5d79e434468fb4a2d037 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c8774f1ef75f8fd541a0cb2bc4e0a739.setIcon(icon_abb4659e99eb5d79e434468fb4a2d037);
        
    
            marker_c8774f1ef75f8fd541a0cb2bc4e0a739.bindTooltip(
                `<div>
                     STARTUPS: Wesabe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_31003827d95b4a2806d84b884a3408a3 = L.marker(
                [37.78976, -122.402524],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_696ae90cc44991b0dd85373e9d8eb827 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_31003827d95b4a2806d84b884a3408a3.setIcon(icon_696ae90cc44991b0dd85373e9d8eb827);
        
    
            marker_31003827d95b4a2806d84b884a3408a3.bindTooltip(
                `<div>
                     STARTUPS: Prosper
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_70c4b29af18d9cc86e33bd3c77ea8f8c = L.marker(
                [37.789803, -122.389757],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ef72cc31443aab55ee76b4317e6ea0c7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_70c4b29af18d9cc86e33bd3c77ea8f8c.setIcon(icon_ef72cc31443aab55ee76b4317e6ea0c7);
        
    
            marker_70c4b29af18d9cc86e33bd3c77ea8f8c.bindTooltip(
                `<div>
                     STARTUPS: Google
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_efb7bccb3c5c7c75c41fa51db4a777cd = L.marker(
                [37.392936, -122.07948],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_333b8e30b11f2919cf9ad55c88fa2e36 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_efb7bccb3c5c7c75c41fa51db4a777cd.setIcon(icon_333b8e30b11f2919cf9ad55c88fa2e36);
        
    
            marker_efb7bccb3c5c7c75c41fa51db4a777cd.bindTooltip(
                `<div>
                     STARTUPS: Ustream
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_feae0aaa38cc274a9cba35672364fa35 = L.marker(
                [37.757758, -122.388243],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_67c11689756bcef44b9b68c75fccab51 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_feae0aaa38cc274a9cba35672364fa35.setIcon(icon_67c11689756bcef44b9b68c75fccab51);
        
    
            marker_feae0aaa38cc274a9cba35672364fa35.bindTooltip(
                `<div>
                     STARTUPS: Revision3
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e0c2e836a9e68de72015f6486fee22f6 = L.marker(
                [37.780716, -122.393913],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3949fd7e9fe355618417bf627714ad21 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e0c2e836a9e68de72015f6486fee22f6.setIcon(icon_3949fd7e9fe355618417bf627714ad21);
        
    
            marker_e0c2e836a9e68de72015f6486fee22f6.bindTooltip(
                `<div>
                     STARTUPS: CastTV
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_07245366a2bfce7a9b8639f4d6c23133 = L.marker(
                [37.780134, -122.396744],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_fec63fd8fc559a5acb3e45bc5ff9eda2 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_07245366a2bfce7a9b8639f4d6c23133.setIcon(icon_fec63fd8fc559a5acb3e45bc5ff9eda2);
        
    
            marker_07245366a2bfce7a9b8639f4d6c23133.bindTooltip(
                `<div>
                     STARTUPS: iSkoot
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_26fffae5ba56199e8e17749351b62246 = L.marker(
                [37.7870842, -122.4038047],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cf803952423c45bf9c01255c2f2f6aa8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_26fffae5ba56199e8e17749351b62246.setIcon(icon_cf803952423c45bf9c01255c2f2f6aa8);
        
    
            marker_26fffae5ba56199e8e17749351b62246.bindTooltip(
                `<div>
                     STARTUPS: Mashery
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d49dd2263443cb7fcabf81d77bc0d512 = L.marker(
                [37.786183, -122.402195],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_06bcb9954845d685f2f59a0272e5eef6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d49dd2263443cb7fcabf81d77bc0d512.setIcon(icon_06bcb9954845d685f2f59a0272e5eef6);
        
    
            marker_d49dd2263443cb7fcabf81d77bc0d512.bindTooltip(
                `<div>
                     STARTUPS: Yelp
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_0afb6cd78fa2720e0b123bb05803a102 = L.marker(
                [37.7891086, -122.4016144],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ac00f1c1abce9244a28a40447c7c63ec = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_0afb6cd78fa2720e0b123bb05803a102.setIcon(icon_ac00f1c1abce9244a28a40447c7c63ec);
        
    
            marker_0afb6cd78fa2720e0b123bb05803a102.bindTooltip(
                `<div>
                     STARTUPS: KickApps
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d6ef403385d7b9ea6c612e19b8cd68bf = L.marker(
                [37.758113, -122.414689],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_12d28b9800b522f9ecfcc5bcb0394387 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d6ef403385d7b9ea6c612e19b8cd68bf.setIcon(icon_12d28b9800b522f9ecfcc5bcb0394387);
        
    
            marker_d6ef403385d7b9ea6c612e19b8cd68bf.bindTooltip(
                `<div>
                     STARTUPS: Pageflakes
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_0fb5337c58846b92a5ef77671b45022d = L.marker(
                [37.781427, -122.392144],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4589c410e0c986a997dc5b4963f93dd7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_0fb5337c58846b92a5ef77671b45022d.setIcon(icon_4589c410e0c986a997dc5b4963f93dd7);
        
    
            marker_0fb5337c58846b92a5ef77671b45022d.bindTooltip(
                `<div>
                     STARTUPS: Slide
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4da96e00a2a98273cfd9f4287b3a2316 = L.marker(
                [37.782103, -122.401116],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f5332e5c645a1c4f51328ed702d486c7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4da96e00a2a98273cfd9f4287b3a2316.setIcon(icon_f5332e5c645a1c4f51328ed702d486c7);
        
    
            marker_4da96e00a2a98273cfd9f4287b3a2316.bindTooltip(
                `<div>
                     STARTUPS: Bebo
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_573c4f4db932a26e8beac573ed7df754 = L.marker(
                [37.779026, -122.40184],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_20400f3a787e1e7418278c57fbef9f76 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_573c4f4db932a26e8beac573ed7df754.setIcon(icon_20400f3a787e1e7418278c57fbef9f76);
        
    
            marker_573c4f4db932a26e8beac573ed7df754.bindTooltip(
                `<div>
                     STARTUPS: adBrite
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8d5ec5c88d3ad4f75120d3296600c855 = L.marker(
                [37.796396, -122.404869],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_047374f309eb4c22c24d154248c6bcb2 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8d5ec5c88d3ad4f75120d3296600c855.setIcon(icon_047374f309eb4c22c24d154248c6bcb2);
        
    
            marker_8d5ec5c88d3ad4f75120d3296600c855.bindTooltip(
                `<div>
                     STARTUPS: Loomia
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3b4f47b83c2049ccc1f9f11a5cd969e1 = L.marker(
                [37.779507, -122.39071],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_6e8bd8b79f545dab2c37c650f33adc9b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3b4f47b83c2049ccc1f9f11a5cd969e1.setIcon(icon_6e8bd8b79f545dab2c37c650f33adc9b);
        
    
            marker_3b4f47b83c2049ccc1f9f11a5cd969e1.bindTooltip(
                `<div>
                     STARTUPS: Eventbrite
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_32392f137625e1de422a8884b9fd5015 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c96ed107d37dd1a6e7771bca4b4ad4ba = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_32392f137625e1de422a8884b9fd5015.setIcon(icon_c96ed107d37dd1a6e7771bca4b4ad4ba);
        
    
            marker_32392f137625e1de422a8884b9fd5015.bindTooltip(
                `<div>
                     STARTUPS: GigaOM
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9803c6cde00457c87e9ff7e076b9aed9 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_82ab16a8c8289d4ab1b6dee13d2841c6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9803c6cde00457c87e9ff7e076b9aed9.setIcon(icon_82ab16a8c8289d4ab1b6dee13d2841c6);
        
    
            marker_9803c6cde00457c87e9ff7e076b9aed9.bindTooltip(
                `<div>
                     STARTUPS: Seesmic
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_929efde931105c9e60ea94c43fc6b7ca = L.marker(
                [37.782263, -122.392142],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5dd4a6d29cf5433c494bd715671703da = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_929efde931105c9e60ea94c43fc6b7ca.setIcon(icon_5dd4a6d29cf5433c494bd715671703da);
        
    
            marker_929efde931105c9e60ea94c43fc6b7ca.bindTooltip(
                `<div>
                     STARTUPS: GoingOn
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f6d68147ff1445b15742bd72a2ab291e = L.marker(
                [37.766909, -122.406676],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_48e1d77cc08090bebc6b2ff44f948bd6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f6d68147ff1445b15742bd72a2ab291e.setIcon(icon_48e1d77cc08090bebc6b2ff44f948bd6);
        
    
            marker_f6d68147ff1445b15742bd72a2ab291e.bindTooltip(
                `<div>
                     STARTUPS: Flixster
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_eda1f949bdfb7d27347de4218bf491d9 = L.marker(
                [37.793703, -122.398479],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_139df9c05f6a0eae6c744bbdce3e2fff = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_eda1f949bdfb7d27347de4218bf491d9.setIcon(icon_139df9c05f6a0eae6c744bbdce3e2fff);
        
    
            marker_eda1f949bdfb7d27347de4218bf491d9.bindTooltip(
                `<div>
                     STARTUPS: Piczo
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c46bde2bdfe97a6d634dbfa031117214 = L.marker(
                [37.779558, -122.393041],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_943df94986a43b6b0b8fb1f51d1a01eb = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c46bde2bdfe97a6d634dbfa031117214.setIcon(icon_943df94986a43b6b0b8fb1f51d1a01eb);
        
    
            marker_c46bde2bdfe97a6d634dbfa031117214.bindTooltip(
                `<div>
                     STARTUPS: PowerReviews
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_0b649e7acc41231dbf03803a117a341f = L.marker(
                [37.788668, -122.400558],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8d1062c0c9c20a39f37fe427daba24a8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_0b649e7acc41231dbf03803a117a341f.setIcon(icon_8d1062c0c9c20a39f37fe427daba24a8);
        
    
            marker_0b649e7acc41231dbf03803a117a341f.bindTooltip(
                `<div>
                     STARTUPS: hi5
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_7ca158ddf915118c17f78fa8b12dae01 = L.marker(
                [37.7753, -122.4186],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e62eee803456f2c81ff78e394fdc8e8a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_7ca158ddf915118c17f78fa8b12dae01.setIcon(icon_e62eee803456f2c81ff78e394fdc8e8a);
        
    
            marker_7ca158ddf915118c17f78fa8b12dae01.bindTooltip(
                `<div>
                     STARTUPS: Tagged
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e4ab14d489a2afc660403873d7ef58f1 = L.marker(
                [37.787092, -122.399972],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e5d52629975575bbcc34e3df5c6bdc04 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e4ab14d489a2afc660403873d7ef58f1.setIcon(icon_e5d52629975575bbcc34e3df5c6bdc04);
        
    
            marker_e4ab14d489a2afc660403873d7ef58f1.bindTooltip(
                `<div>
                     STARTUPS: Curse
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_ace73964ad751fa711fe610cdede3db7 = L.marker(
                [37.787683, -122.410943],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8bc95374372a5f336c634c7263c4cae1 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_ace73964ad751fa711fe610cdede3db7.setIcon(icon_8bc95374372a5f336c634c7263c4cae1);
        
    
            marker_ace73964ad751fa711fe610cdede3db7.bindTooltip(
                `<div>
                     STARTUPS: eBuddy
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_342514a960b35e5de8b88d1137e95ca1 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_985da3cf12160221c0f56b2eee87e248 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_342514a960b35e5de8b88d1137e95ca1.setIcon(icon_985da3cf12160221c0f56b2eee87e248);
        
    
            marker_342514a960b35e5de8b88d1137e95ca1.bindTooltip(
                `<div>
                     STARTUPS: Grockit
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_447b78a8a07f2e58de874f10087cecdf = L.marker(
                [37.792394, -122.402349],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a58d4df5d8738ad7b1cd8b772a763256 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_447b78a8a07f2e58de874f10087cecdf.setIcon(icon_a58d4df5d8738ad7b1cd8b772a763256);
        
    
            marker_447b78a8a07f2e58de874f10087cecdf.bindTooltip(
                `<div>
                     STARTUPS: Kiptronic
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_018febdd31bdb492efa06d4395101e97 = L.marker(
                [37.78554, -122.396689],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8e88018bda3d3f93567ac695d77c2397 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_018febdd31bdb492efa06d4395101e97.setIcon(icon_8e88018bda3d3f93567ac695d77c2397);
        
    
            marker_018febdd31bdb492efa06d4395101e97.bindTooltip(
                `<div>
                     STARTUPS: Snapfish
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1eb57cdb6108180f5c34f547797f8c5a = L.marker(
                [40.270666, -74.761403],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cdf03237eca88aee303849b72eb24e9a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1eb57cdb6108180f5c34f547797f8c5a.setIcon(icon_cdf03237eca88aee303849b72eb24e9a);
        
    
            marker_1eb57cdb6108180f5c34f547797f8c5a.bindTooltip(
                `<div>
                     STARTUPS: Scrybe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3f192814158738f5e8bbd9dcc7c150b1 = L.marker(
                [51.500152, -0.126236],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_853e36b0eab00ee9641ec4307a0a601e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3f192814158738f5e8bbd9dcc7c150b1.setIcon(icon_853e36b0eab00ee9641ec4307a0a601e);
        
    
            marker_3f192814158738f5e8bbd9dcc7c150b1.bindTooltip(
                `<div>
                     STARTUPS: Viagogo
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_260dd4ca6289cd8f2806edb56de4be85 = L.marker(
                [37.437328, -122.159928],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_20c79f8f8fa069d52cfa5f04792c12ad = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_260dd4ca6289cd8f2806edb56de4be85.setIcon(icon_20c79f8f8fa069d52cfa5f04792c12ad);
        
    
            marker_260dd4ca6289cd8f2806edb56de4be85.bindTooltip(
                `<div>
                     STARTUPS: Metacafe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_755c481259df8768101389ac0c09d50d = L.marker(
                [37.767575, -122.411157],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5e1136faf8f37feb62baf79d7e623504 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_755c481259df8768101389ac0c09d50d.setIcon(icon_5e1136faf8f37feb62baf79d7e623504);
        
    
            marker_755c481259df8768101389ac0c09d50d.bindTooltip(
                `<div>
                     STARTUPS: Kontera
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_ff1f73bc181f3201a34840ea88445c18 = L.marker(
                [37.7909427, -122.4084994],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a0663adc640e5f6780e8ce4589787a9c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_ff1f73bc181f3201a34840ea88445c18.setIcon(icon_a0663adc640e5f6780e8ce4589787a9c);
        
    
            marker_ff1f73bc181f3201a34840ea88445c18.bindTooltip(
                `<div>
                     STARTUPS: Dropbox
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_50872e0b2b7799c8af8d65c993802bb6 = L.marker(
                [37.779507, -122.39071],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_dcf3530da9086cb46ed60eb13a4e9ddd = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_50872e0b2b7799c8af8d65c993802bb6.setIcon(icon_dcf3530da9086cb46ed60eb13a4e9ddd);
        
    
            marker_50872e0b2b7799c8af8d65c993802bb6.bindTooltip(
                `<div>
                     STARTUPS: TokBox
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_006f73aa9d3f4cc68cf37955fac7c59a = L.marker(
                [37.7911148, -122.3954751],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e1357e40a4bb5a4bbbf69a6f4c478c24 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_006f73aa9d3f4cc68cf37955fac7c59a.setIcon(icon_e1357e40a4bb5a4bbbf69a6f4c478c24);
        
    
            marker_006f73aa9d3f4cc68cf37955fac7c59a.bindTooltip(
                `<div>
                     STARTUPS: Yola
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4d381445ab85ac081b6d9cd98edf5d7b = L.marker(
                [37.790998, -122.394668],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_bb019e273e38ebe927c9a1ca294be4e6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4d381445ab85ac081b6d9cd98edf5d7b.setIcon(icon_bb019e273e38ebe927c9a1ca294be4e6);
        
    
            marker_4d381445ab85ac081b6d9cd98edf5d7b.bindTooltip(
                `<div>
                     STARTUPS: BitTorrent
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_0fb7ac9986936d7db1d08a09590b81da = L.marker(
                [37.786942, -122.401245],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8ca633c82fd39cee96f7f588cab5349a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_0fb7ac9986936d7db1d08a09590b81da.setIcon(icon_8ca633c82fd39cee96f7f588cab5349a);
        
    
            marker_0fb7ac9986936d7db1d08a09590b81da.bindTooltip(
                `<div>
                     STARTUPS: Kongregate
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_38c8aa98d6712ad7a311ba892670339a = L.marker(
                [37.781557, -122.407959],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5b2cc18b4081945fe47bc04c67148707 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_38c8aa98d6712ad7a311ba892670339a.setIcon(icon_5b2cc18b4081945fe47bc04c67148707);
        
    
            marker_38c8aa98d6712ad7a311ba892670339a.bindTooltip(
                `<div>
                     STARTUPS: DanceJam
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_50103fa059170484fef4318ce86e10d6 = L.marker(
                [37.762125, -122.4147],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ea6e8323227cc29c35493ec33e623b4a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_50103fa059170484fef4318ce86e10d6.setIcon(icon_ea6e8323227cc29c35493ec33e623b4a);
        
    
            marker_50103fa059170484fef4318ce86e10d6.bindTooltip(
                `<div>
                     STARTUPS: Kiva
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_080d2580fc1286ed134a860089a3e8c3 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_928ccea111a8be703e96a3f129b430ec = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_080d2580fc1286ed134a860089a3e8c3.setIcon(icon_928ccea111a8be703e96a3f129b430ec);
        
    
            marker_080d2580fc1286ed134a860089a3e8c3.bindTooltip(
                `<div>
                     STARTUPS: Fathom Online
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4bf6d99e1048d4f67044ec605f5b44b7 = L.marker(
                [37.789488, -122.397593],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b295f18504ee5e0721843885a4aa2ea0 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4bf6d99e1048d4f67044ec605f5b44b7.setIcon(icon_b295f18504ee5e0721843885a4aa2ea0);
        
    
            marker_4bf6d99e1048d4f67044ec605f5b44b7.bindTooltip(
                `<div>
                     STARTUPS: Zivity
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_244a1040b9d5ec241bbd10befa0aa2c6 = L.marker(
                [37.09024, -95.712891],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9de4d471ec5f21d44883d45b9fd71cd7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_244a1040b9d5ec241bbd10befa0aa2c6.setIcon(icon_9de4d471ec5f21d44883d45b9fd71cd7);
        
    
            marker_244a1040b9d5ec241bbd10befa0aa2c6.bindTooltip(
                `<div>
                     STARTUPS: DotBlu
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_22ecd7350c1c7cfb2ce3cf07b4353d08 = L.marker(
                [37.782263, -122.392142],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b5516ae7d78341d46ad848fd74f47243 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_22ecd7350c1c7cfb2ce3cf07b4353d08.setIcon(icon_b5516ae7d78341d46ad848fd74f47243);
        
    
            marker_22ecd7350c1c7cfb2ce3cf07b4353d08.bindTooltip(
                `<div>
                     STARTUPS: Mevio
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d7f66c3f41d1919d2fca871fa55b75fc = L.marker(
                [37.790964, -122.401128],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4720fa055f54000d7e1d6e96f327f6d9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d7f66c3f41d1919d2fca871fa55b75fc.setIcon(icon_4720fa055f54000d7e1d6e96f327f6d9);
        
    
            marker_d7f66c3f41d1919d2fca871fa55b75fc.bindTooltip(
                `<div>
                     STARTUPS: Atom Entertainment
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9c6c1469ad3818a5190995233c2cf4e1 = L.marker(
                [37.710677, -122.39323],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e5d996dfd1c646d47ac2b0bfb9a28d9b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9c6c1469ad3818a5190995233c2cf4e1.setIcon(icon_e5d996dfd1c646d47ac2b0bfb9a28d9b);
        
    
            marker_9c6c1469ad3818a5190995233c2cf4e1.bindTooltip(
                `<div>
                     STARTUPS: Greystripe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cfaed47720ee1075fd3d8b94846d073e = L.marker(
                [37.766909, -122.406676],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7608878f757024ebe5d492ecf65eb2dd = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cfaed47720ee1075fd3d8b94846d073e.setIcon(icon_7608878f757024ebe5d492ecf65eb2dd);
        
    
            marker_cfaed47720ee1075fd3d8b94846d073e.bindTooltip(
                `<div>
                     STARTUPS: Trulia
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8249dac6ef811c97d16c7e74d56c2313 = L.marker(
                [37.785647, -122.405265],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_36c6806be6961997badf1aee22ee4e87 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8249dac6ef811c97d16c7e74d56c2313.setIcon(icon_36c6806be6961997badf1aee22ee4e87);
        
    
            marker_8249dac6ef811c97d16c7e74d56c2313.bindTooltip(
                `<div>
                     STARTUPS: OpenTable
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cd033ef0f0755d0bdff91522e6615a01 = L.marker(
                [37.78866, -122.401406],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d9a611fbd1e7444008df0abc609f9a2e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cd033ef0f0755d0bdff91522e6615a01.setIcon(icon_d9a611fbd1e7444008df0abc609f9a2e);
        
    
            marker_cd033ef0f0755d0bdff91522e6615a01.bindTooltip(
                `<div>
                     STARTUPS: Adknowledge
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f8d2977748207747ec8fcb0c9da2e4a5 = L.marker(
                [37.781265, -122.393229],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_68821d6e18f58dbc36d7603215a862c5 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f8d2977748207747ec8fcb0c9da2e4a5.setIcon(icon_68821d6e18f58dbc36d7603215a862c5);
        
    
            marker_f8d2977748207747ec8fcb0c9da2e4a5.bindTooltip(
                `<div>
                     STARTUPS: Crunchyroll
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_13711a5500a2b872de432d02b7bf494f = L.marker(
                [37.779624, -122.391236],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_73842acb41b0ce25eedae74e87826b21 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_13711a5500a2b872de432d02b7bf494f.setIcon(icon_73842acb41b0ce25eedae74e87826b21);
        
    
            marker_13711a5500a2b872de432d02b7bf494f.bindTooltip(
                `<div>
                     STARTUPS: imeem
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_6e0abb55c370aa8c48abfa045c934bcd = L.marker(
                [37.764169, -122.402021],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_91bc46e50a1599e96264630d31122eb4 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_6e0abb55c370aa8c48abfa045c934bcd.setIcon(icon_91bc46e50a1599e96264630d31122eb4);
        
    
            marker_6e0abb55c370aa8c48abfa045c934bcd.bindTooltip(
                `<div>
                     STARTUPS: TripIt
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_37c27def9e576be254ee5da2ff9ba547 = L.marker(
                [37.768708, -122.402866],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4751c7a5c7366c2c4db83fc65e8d4bab = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_37c27def9e576be254ee5da2ff9ba547.setIcon(icon_4751c7a5c7366c2c4db83fc65e8d4bab);
        
    
            marker_37c27def9e576be254ee5da2ff9ba547.bindTooltip(
                `<div>
                     STARTUPS: Jawbone
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cf6705a54e184f487be2ce26f9fcee07 = L.marker(
                [37.789634, -122.404052],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_977d53d35cff8f729605ed5f20f5f3f8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cf6705a54e184f487be2ce26f9fcee07.setIcon(icon_977d53d35cff8f729605ed5f20f5f3f8);
        
    
            marker_cf6705a54e184f487be2ce26f9fcee07.bindTooltip(
                `<div>
                     STARTUPS: Xobni
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a08aaff7980119f3481b7d757b68429b = L.marker(
                [37.765125, -122.4044034],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cd31d5f4afa0a014b94c430506ab9494 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a08aaff7980119f3481b7d757b68429b.setIcon(icon_cd31d5f4afa0a014b94c430506ab9494);
        
    
            marker_a08aaff7980119f3481b7d757b68429b.bindTooltip(
                `<div>
                     STARTUPS: 3Jam
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_778105bbdcadb4a7d811fd8c3c23cbca = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_00b371351070d49d9d4c9c0b582c7be1 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_778105bbdcadb4a7d811fd8c3c23cbca.setIcon(icon_00b371351070d49d9d4c9c0b582c7be1);
        
    
            marker_778105bbdcadb4a7d811fd8c3c23cbca.bindTooltip(
                `<div>
                     STARTUPS: Zannel
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_53417024cbd83f42ffaa0f3cd21a877e = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_6a2e812417758e6da31ddc15da3624fe = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_53417024cbd83f42ffaa0f3cd21a877e.setIcon(icon_6a2e812417758e6da31ddc15da3624fe);
        
    
            marker_53417024cbd83f42ffaa0f3cd21a877e.bindTooltip(
                `<div>
                     STARTUPS: Wikinvest
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_0e92baf0162e7ff8aff8b4d158e1cbdf = L.marker(
                [37.783898, -122.395234],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_6c0969a5b9633725a0e3af0c864c1290 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_0e92baf0162e7ff8aff8b4d158e1cbdf.setIcon(icon_6c0969a5b9633725a0e3af0c864c1290);
        
    
            marker_0e92baf0162e7ff8aff8b4d158e1cbdf.bindTooltip(
                `<div>
                     STARTUPS: Rupture
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d6bc6ff48d030ad4dd208bd763b30142 = L.marker(
                [37.856331, -122.495222],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d02a0e7298b7bcecc0f1890155c104a4 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d6bc6ff48d030ad4dd208bd763b30142.setIcon(icon_d02a0e7298b7bcecc0f1890155c104a4);
        
    
            marker_d6bc6ff48d030ad4dd208bd763b30142.bindTooltip(
                `<div>
                     STARTUPS: Whiskey Media
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b15ede06fcadfc3e1f2d3a4475b3905b = L.marker(
                [37.792968, -122.40414],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cc3c189e8b42cb39ce54a2c695e5f194 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b15ede06fcadfc3e1f2d3a4475b3905b.setIcon(icon_cc3c189e8b42cb39ce54a2c695e5f194);
        
    
            marker_b15ede06fcadfc3e1f2d3a4475b3905b.bindTooltip(
                `<div>
                     STARTUPS: Blurb
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f23fbd19dc4e8719066d1111c490e39a = L.marker(
                [37.782809, -122.394354],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f494b764ae374dd7ec25753693c03fe8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f23fbd19dc4e8719066d1111c490e39a.setIcon(icon_f494b764ae374dd7ec25753693c03fe8);
        
    
            marker_f23fbd19dc4e8719066d1111c490e39a.bindTooltip(
                `<div>
                     STARTUPS: The Climate Corporation
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_251e4856ef5eab3710a90b67cf47d676 = L.marker(
                [37.744274, -122.421344],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_6c29f36d282d80c91face6120318d29a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_251e4856ef5eab3710a90b67cf47d676.setIcon(icon_6c29f36d282d80c91face6120318d29a);
        
    
            marker_251e4856ef5eab3710a90b67cf47d676.bindTooltip(
                `<div>
                     STARTUPS: Automattic
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_6d59eaede6907430f66878c89523e082 = L.marker(
                [37.7753, -122.398058],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7624803c083a86df0fa84146d7c55d9d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_6d59eaede6907430f66878c89523e082.setIcon(icon_7624803c083a86df0fa84146d7c55d9d);
        
    
            marker_6d59eaede6907430f66878c89523e082.bindTooltip(
                `<div>
                     STARTUPS: Radar Networks
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_97ce08490fb81b38601ce126e496ba81 = L.marker(
                [37.787249, -122.3990501],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f85cb85d3de071a4d36b3ed6528c6c14 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_97ce08490fb81b38601ce126e496ba81.setIcon(icon_f85cb85d3de071a4d36b3ed6528c6c14);
        
    
            marker_97ce08490fb81b38601ce126e496ba81.bindTooltip(
                `<div>
                     STARTUPS: eXelate
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8f9d8618d70099a379ad9d4174798298 = L.marker(
                [37.77759, -122.395602],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e76a85ee8ea91b984f4de3dbbe8289ff = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8f9d8618d70099a379ad9d4174798298.setIcon(icon_e76a85ee8ea91b984f4de3dbbe8289ff);
        
    
            marker_8f9d8618d70099a379ad9d4174798298.bindTooltip(
                `<div>
                     STARTUPS: Socializr
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c2af9d0f74d66c03a2863e62bfea7fe1 = L.marker(
                [37.787126, -122.39783],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d64ecf3b80ffe4c349d37316ac95ce3b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c2af9d0f74d66c03a2863e62bfea7fe1.setIcon(icon_d64ecf3b80ffe4c349d37316ac95ce3b);
        
    
            marker_c2af9d0f74d66c03a2863e62bfea7fe1.bindTooltip(
                `<div>
                     STARTUPS: Songbird
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1a21d7072b6e56bfe369c02bbdae7029 = L.marker(
                [37.786906, -122.397672],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_46023cbfea461410084faae0e67d28bc = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1a21d7072b6e56bfe369c02bbdae7029.setIcon(icon_46023cbfea461410084faae0e67d28bc);
        
    
            marker_1a21d7072b6e56bfe369c02bbdae7029.bindTooltip(
                `<div>
                     STARTUPS: Disqus
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_73439942e48b316d42c3a4b47d275426 = L.marker(
                [37.794643, -122.401144],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_0c7a25f3756b12a01657010daef23a7e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_73439942e48b316d42c3a4b47d275426.setIcon(icon_0c7a25f3756b12a01657010daef23a7e);
        
    
            marker_73439942e48b316d42c3a4b47d275426.bindTooltip(
                `<div>
                     STARTUPS: BrightRoll
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b53fc57d3cfa21c02a28d9c559d4c569 = L.marker(
                [37.762162, -122.413912],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_6b37e2de64d4f0307021ebd2675de36b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b53fc57d3cfa21c02a28d9c559d4c569.setIcon(icon_6b37e2de64d4f0307021ebd2675de36b);
        
    
            marker_b53fc57d3cfa21c02a28d9c559d4c569.bindTooltip(
                `<div>
                     STARTUPS: Doppelganger
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3d2ddd8281fe5e9a086a9dda4435c611 = L.marker(
                [37.78554, -122.396689],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_407ce825f1350ad457a146ee48bcdbc1 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3d2ddd8281fe5e9a086a9dda4435c611.setIcon(icon_407ce825f1350ad457a146ee48bcdbc1);
        
    
            marker_3d2ddd8281fe5e9a086a9dda4435c611.bindTooltip(
                `<div>
                     STARTUPS: Adteractive
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_28dd580b294b7bc62604579c6472034b = L.marker(
                [37.803847, -122.402468],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8f0f5df2f1ebf47c7eea284c279899c4 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_28dd580b294b7bc62604579c6472034b.setIcon(icon_8f0f5df2f1ebf47c7eea284c279899c4);
        
    
            marker_28dd580b294b7bc62604579c6472034b.bindTooltip(
                `<div>
                     STARTUPS: GameLayers
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_994c331055b1d75f1ca26bb79978850b = L.marker(
                [33.754487, -84.389663],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_497306910516c48a937814ad7c07dac2 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_994c331055b1d75f1ca26bb79978850b.setIcon(icon_497306910516c48a937814ad7c07dac2);
        
    
            marker_994c331055b1d75f1ca26bb79978850b.bindTooltip(
                `<div>
                     STARTUPS: beRecruited
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a8949356076f231dc5dc2ed6b54f63e7 = L.marker(
                [37.78274, -122.390945],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8c9d4cbf03a5405a982013287b9ca213 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a8949356076f231dc5dc2ed6b54f63e7.setIcon(icon_8c9d4cbf03a5405a982013287b9ca213);
        
    
            marker_a8949356076f231dc5dc2ed6b54f63e7.bindTooltip(
                `<div>
                     STARTUPS: Splunk
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8ab52a55432b76b87b18cc0b6c0879c5 = L.marker(
                [37.7766184, -122.3938931],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e78fae516bcde29a5eebad476c60abf8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8ab52a55432b76b87b18cc0b6c0879c5.setIcon(icon_e78fae516bcde29a5eebad476c60abf8);
        
    
            marker_8ab52a55432b76b87b18cc0b6c0879c5.bindTooltip(
                `<div>
                     STARTUPS: Dapper
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d15e14f26845aacf27765e730c2f64a5 = L.marker(
                [37.09024, -95.712891],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a43d06f0aec45b88aea773f6d59dd232 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d15e14f26845aacf27765e730c2f64a5.setIcon(icon_a43d06f0aec45b88aea773f6d59dd232);
        
    
            marker_d15e14f26845aacf27765e730c2f64a5.bindTooltip(
                `<div>
                     STARTUPS: Masala
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_53cb9aa576bcdfe5fc98986f179dcb23 = L.marker(
                [37.7749295, -122.4194155],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a1023d54df6b78b952c4d8ec4f2c5dab = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_53cb9aa576bcdfe5fc98986f179dcb23.setIcon(icon_a1023d54df6b78b952c4d8ec4f2c5dab);
        
    
            marker_53cb9aa576bcdfe5fc98986f179dcb23.bindTooltip(
                `<div>
                     STARTUPS: Uversity
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3a0e2345e64b41cb3018e02f15128761 = L.marker(
                [37.7876952, -122.3991505],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_91ff04defc86d74d828cf1a832559869 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3a0e2345e64b41cb3018e02f15128761.setIcon(icon_91ff04defc86d74d828cf1a832559869);
        
    
            marker_3a0e2345e64b41cb3018e02f15128761.bindTooltip(
                `<div>
                     STARTUPS: HubPages
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_44ebdb2b53791e9826f3d96b58e15621 = L.marker(
                [37.790346, -122.40185],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_035ea6b43e19bfaadc66347039d22237 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_44ebdb2b53791e9826f3d96b58e15621.setIcon(icon_035ea6b43e19bfaadc66347039d22237);
        
    
            marker_44ebdb2b53791e9826f3d96b58e15621.bindTooltip(
                `<div>
                     STARTUPS: PlayFirst
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4e8170e0f9700e2f75dbf4dd9d839949 = L.marker(
                [37.559836, -122.271532],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b897238b9b90647b2d119b6c936b173c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4e8170e0f9700e2f75dbf4dd9d839949.setIcon(icon_b897238b9b90647b2d119b6c936b173c);
        
    
            marker_4e8170e0f9700e2f75dbf4dd9d839949.bindTooltip(
                `<div>
                     STARTUPS: Deem
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2e0f74c074da73ea9767eb2a2d7c0e6a = L.marker(
                [37.870258, -122.272513],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_dcf22baa828ab7a315f9fc2f57644eb0 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2e0f74c074da73ea9767eb2a2d7c0e6a.setIcon(icon_dcf22baa828ab7a315f9fc2f57644eb0);
        
    
            marker_2e0f74c074da73ea9767eb2a2d7c0e6a.bindTooltip(
                `<div>
                     STARTUPS: Causes
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_af06e44fcafd7ace6fd829279fd6a000 = L.marker(
                [37.771706, -122.402683],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2b3f211e6cc11d7c7c3cbaaa5a5ca250 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_af06e44fcafd7ace6fd829279fd6a000.setIcon(icon_2b3f211e6cc11d7c7c3cbaaa5a5ca250);
        
    
            marker_af06e44fcafd7ace6fd829279fd6a000.bindTooltip(
                `<div>
                     STARTUPS: Shozu
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3d776e25a49c3ba626406e1a5cb72c33 = L.marker(
                [25.752358, -80.25468],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_93588917913fb83bf3ec404b25933709 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3d776e25a49c3ba626406e1a5cb72c33.setIcon(icon_93588917913fb83bf3ec404b25933709);
        
    
            marker_3d776e25a49c3ba626406e1a5cb72c33.bindTooltip(
                `<div>
                     STARTUPS: Scrapblog
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3fc6913f484b2521d1851074dd556b77 = L.marker(
                [37.392443, -122.072517],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c6b76ecb519fdb2ccd2d5501e8293546 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3fc6913f484b2521d1851074dd556b77.setIcon(icon_c6b76ecb519fdb2ccd2d5501e8293546);
        
    
            marker_3fc6913f484b2521d1851074dd556b77.bindTooltip(
                `<div>
                     STARTUPS: Meraki
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_59f3004577ea04d687ed7164777e2809 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2353d66aff7b360fa327567d607650da = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_59f3004577ea04d687ed7164777e2809.setIcon(icon_2353d66aff7b360fa327567d607650da);
        
    
            marker_59f3004577ea04d687ed7164777e2809.bindTooltip(
                `<div>
                     STARTUPS: Outspark
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_bc25afd7647cee52266635950fe7c71b = L.marker(
                [37.856743, -122.480153],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9eb4a64885e1fd0afdf6957412e03722 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_bc25afd7647cee52266635950fe7c71b.setIcon(icon_9eb4a64885e1fd0afdf6957412e03722);
        
    
            marker_bc25afd7647cee52266635950fe7c71b.bindTooltip(
                `<div>
                     STARTUPS: Joyent
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c4692429aa7571c69f134f44b83ef06e = L.marker(
                [37.795191, -122.430411],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_af7560aa071c8d9e8e78f02b02dd7e1c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c4692429aa7571c69f134f44b83ef06e.setIcon(icon_af7560aa071c8d9e8e78f02b02dd7e1c);
        
    
            marker_c4692429aa7571c69f134f44b83ef06e.bindTooltip(
                `<div>
                     STARTUPS: NuConomy
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_144ddbb4192f5771a494405bce3c683f = L.marker(
                [37.798853, -122.398599],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_76fcccd3f2255ec01bc662a8b3997a57 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_144ddbb4192f5771a494405bce3c683f.setIcon(icon_76fcccd3f2255ec01bc662a8b3997a57);
        
    
            marker_144ddbb4192f5771a494405bce3c683f.bindTooltip(
                `<div>
                     STARTUPS: Widgetbox
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_fbb69521c104c1e8a821f484d5f2f86b = L.marker(
                [37.765158, -122.404234],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_eb07d1939262c22c723514c5b45356e6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_fbb69521c104c1e8a821f484d5f2f86b.setIcon(icon_eb07d1939262c22c723514c5b45356e6);
        
    
            marker_fbb69521c104c1e8a821f484d5f2f86b.bindTooltip(
                `<div>
                     STARTUPS: Zynga
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_537bc5325e6485832d8bd1380da9f251 = L.marker(
                [37.7891288, -122.4020276],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e21653298cb0750427a872b716a3ba08 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_537bc5325e6485832d8bd1380da9f251.setIcon(icon_e21653298cb0750427a872b716a3ba08);
        
    
            marker_537bc5325e6485832d8bd1380da9f251.bindTooltip(
                `<div>
                     STARTUPS: Roost
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_29f5ab601fad0588aed1d964505f8301 = L.marker(
                [37.2906027, -121.9317853],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_572e957be651bb39c2271b58d7d0d738 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_29f5ab601fad0588aed1d964505f8301.setIcon(icon_572e957be651bb39c2271b58d7d0d738);
        
    
            marker_29f5ab601fad0588aed1d964505f8301.bindTooltip(
                `<div>
                     STARTUPS: clipsync
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cfbc4bda7c80ceb5e6bae21264439c35 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7c2ae3c2d8ed273424ef99f399afd0bb = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cfbc4bda7c80ceb5e6bae21264439c35.setIcon(icon_7c2ae3c2d8ed273424ef99f399afd0bb);
        
    
            marker_cfbc4bda7c80ceb5e6bae21264439c35.bindTooltip(
                `<div>
                     STARTUPS: Heroku
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1a8336bd9f5d1f5fe2359ae3ad6ef291 = L.marker(
                [37.788266, -122.403865],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_aa107318ecbd9db9b8a584801132562e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1a8336bd9f5d1f5fe2359ae3ad6ef291.setIcon(icon_aa107318ecbd9db9b8a584801132562e);
        
    
            marker_1a8336bd9f5d1f5fe2359ae3ad6ef291.bindTooltip(
                `<div>
                     STARTUPS: Lookery
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3f54562075d76e98eed1a27dcc6902c5 = L.marker(
                [37.762512, -122.46649],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_72fb382bc7ede17d92338fe8e8674457 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3f54562075d76e98eed1a27dcc6902c5.setIcon(icon_72fb382bc7ede17d92338fe8e8674457);
        
    
            marker_3f54562075d76e98eed1a27dcc6902c5.bindTooltip(
                `<div>
                     STARTUPS: Craigslist
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e460422fc5ceaa25ce5232c0f1fa9018 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_48a0701c50cccc91377b677d7717ab76 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e460422fc5ceaa25ce5232c0f1fa9018.setIcon(icon_48a0701c50cccc91377b677d7717ab76);
        
    
            marker_e460422fc5ceaa25ce5232c0f1fa9018.bindTooltip(
                `<div>
                     STARTUPS: Three Rings
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_db67b431d37d77947423f2e37d30b27b = L.marker(
                [37.7759675, -122.3931128],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_514df274564866c7920c38be46636e6a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_db67b431d37d77947423f2e37d30b27b.setIcon(icon_514df274564866c7920c38be46636e6a);
        
    
            marker_db67b431d37d77947423f2e37d30b27b.bindTooltip(
                `<div>
                     STARTUPS: Coverity
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_91aba22d1646fe06a584a08e5d0936f3 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_889008a321161703e71705e0bc3f3cb8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_91aba22d1646fe06a584a08e5d0936f3.setIcon(icon_889008a321161703e71705e0bc3f3cb8);
        
    
            marker_91aba22d1646fe06a584a08e5d0936f3.bindTooltip(
                `<div>
                     STARTUPS: doubleTwist
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_6e3045f058e2238534bc05c0185ef47b = L.marker(
                [37.793375, -122.404571],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5e0f7338c69f536031dac8e7d4d73285 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_6e3045f058e2238534bc05c0185ef47b.setIcon(icon_5e0f7338c69f536031dac8e7d4d73285);
        
    
            marker_6e3045f058e2238534bc05c0185ef47b.bindTooltip(
                `<div>
                     STARTUPS: Ad Infuse
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f9196a9bb9afd5a5faa2f669df6942c7 = L.marker(
                [37.787126, -122.39783],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8988fb6e2da3441367509d83e473eac5 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f9196a9bb9afd5a5faa2f669df6942c7.setIcon(icon_8988fb6e2da3441367509d83e473eac5);
        
    
            marker_f9196a9bb9afd5a5faa2f669df6942c7.bindTooltip(
                `<div>
                     STARTUPS: SendMe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a978998a129d10b932bf9919ab00b546 = L.marker(
                [37.781002, -122.406912],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cfbae9d99a44f96c9a0b2551e5188893 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a978998a129d10b932bf9919ab00b546.setIcon(icon_cfbae9d99a44f96c9a0b2551e5188893);
        
    
            marker_a978998a129d10b932bf9919ab00b546.bindTooltip(
                `<div>
                     STARTUPS: Tiny Pictures
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d4c25c3949a67e37ace428b2a1ff1c21 = L.marker(
                [37.783046, -122.394067],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3d3b6d98d9021ef319a047f54e6d51ca = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d4c25c3949a67e37ace428b2a1ff1c21.setIcon(icon_3d3b6d98d9021ef319a047f54e6d51ca);
        
    
            marker_d4c25c3949a67e37ace428b2a1ff1c21.bindTooltip(
                `<div>
                     STARTUPS: Flurry
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_40fe99f567f110018dacefd24368175c = L.marker(
                [37.776467, -122.395792],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3eae6ef5a51a02e0c818e6fc2dd0f430 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_40fe99f567f110018dacefd24368175c.setIcon(icon_3eae6ef5a51a02e0c818e6fc2dd0f430);
        
    
            marker_40fe99f567f110018dacefd24368175c.bindTooltip(
                `<div>
                     STARTUPS: Bleacher Report
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8cd02775439c39654490d842d43e722b = L.marker(
                [37.790485, -122.40094],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9b116b6370090ed4da2f227727d35419 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8cd02775439c39654490d842d43e722b.setIcon(icon_9b116b6370090ed4da2f227727d35419);
        
    
            marker_8cd02775439c39654490d842d43e722b.bindTooltip(
                `<div>
                     STARTUPS: Sportgenic
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8420faf98b629c3c2a9575d08bdd753a = L.marker(
                [37.7955307, -122.4005983],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e7e7426d239687c4aabea9ce1a190afa = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8420faf98b629c3c2a9575d08bdd753a.setIcon(icon_e7e7426d239687c4aabea9ce1a190afa);
        
    
            marker_8420faf98b629c3c2a9575d08bdd753a.bindTooltip(
                `<div>
                     STARTUPS: 99designs
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_fe7dbab66314db6fbfa8acd5ec7e0762 = L.marker(
                [37.787183, -122.397759],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d7fe519cdf11e66f7a47a18e60a58ebf = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_fe7dbab66314db6fbfa8acd5ec7e0762.setIcon(icon_d7fe519cdf11e66f7a47a18e60a58ebf);
        
    
            marker_fe7dbab66314db6fbfa8acd5ec7e0762.bindTooltip(
                `<div>
                     STARTUPS: LiveRail
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_00d5578903c25182d0ffa6b23a3d9f89 = L.marker(
                [37.789268, -122.395184],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c2a86c0de5114b436577647c70990e11 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_00d5578903c25182d0ffa6b23a3d9f89.setIcon(icon_c2a86c0de5114b436577647c70990e11);
        
    
            marker_00d5578903c25182d0ffa6b23a3d9f89.bindTooltip(
                `<div>
                     STARTUPS: OpenDNS
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d4371c842b62375a34e571e70c3fb911 = L.marker(
                [37.793473, -122.402667],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_952d50846670fa0b092de613660b9ce5 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d4371c842b62375a34e571e70c3fb911.setIcon(icon_952d50846670fa0b092de613660b9ce5);
        
    
            marker_d4371c842b62375a34e571e70c3fb911.bindTooltip(
                `<div>
                     STARTUPS: SearchMe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_ab8ded44bd30ac87f6949862e0ad5d0b = L.marker(
                [37.767575, -122.411157],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f076b6e32c81ead335b5cbf156278aae = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_ab8ded44bd30ac87f6949862e0ad5d0b.setIcon(icon_f076b6e32c81ead335b5cbf156278aae);
        
    
            marker_ab8ded44bd30ac87f6949862e0ad5d0b.bindTooltip(
                `<div>
                     STARTUPS: PopularMedia
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_78368cb2ece3bea13fb7b6542606e8ad = L.marker(
                [37.755012, -122.418137],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b81c37fff1874ba5b2f7db59a7e536ca = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_78368cb2ece3bea13fb7b6542606e8ad.setIcon(icon_b81c37fff1874ba5b2f7db59a7e536ca);
        
    
            marker_78368cb2ece3bea13fb7b6542606e8ad.bindTooltip(
                `<div>
                     STARTUPS: Sprout
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_99f3e361eb15413556a977d011c45542 = L.marker(
                [37.791137, -122.391843],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3c6b402c3121a207f74b2f512f6da032 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_99f3e361eb15413556a977d011c45542.setIcon(icon_3c6b402c3121a207f74b2f512f6da032);
        
    
            marker_99f3e361eb15413556a977d011c45542.bindTooltip(
                `<div>
                     STARTUPS: Elastra
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b5b6c145feb5383b94135fedccc499d0 = L.marker(
                [37.7867612, -122.4014585],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_0a151cf13304faa509ff84b7f5747948 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b5b6c145feb5383b94135fedccc499d0.setIcon(icon_0a151cf13304faa509ff84b7f5747948);
        
    
            marker_b5b6c145feb5383b94135fedccc499d0.bindTooltip(
                `<div>
                     STARTUPS: CITTIO
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_44dd1519820c87f317cf20b7bf637b48 = L.marker(
                [37.781689, -122.391061],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_56974ad18c61fba3509bdc815ba3469f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_44dd1519820c87f317cf20b7bf637b48.setIcon(icon_56974ad18c61fba3509bdc815ba3469f);
        
    
            marker_44dd1519820c87f317cf20b7bf637b48.bindTooltip(
                `<div>
                     STARTUPS: Rapt
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_66689a7052a2c646b883127d767deb6f = L.marker(
                [37.796164, -122.420903],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4f46016cbed6535bae7e20a8292e98e9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_66689a7052a2c646b883127d767deb6f.setIcon(icon_4f46016cbed6535bae7e20a8292e98e9);
        
    
            marker_66689a7052a2c646b883127d767deb6f.bindTooltip(
                `<div>
                     STARTUPS: Liftopia
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_909968d61d45ce7a34bf27e084751224 = L.marker(
                [37.796216, -122.403755],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5c5faac3fec73d944a27abea7cc529cb = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_909968d61d45ce7a34bf27e084751224.setIcon(icon_5c5faac3fec73d944a27abea7cc529cb);
        
    
            marker_909968d61d45ce7a34bf27e084751224.bindTooltip(
                `<div>
                     STARTUPS: PopJax
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3e633487a2ca2aab00a62cdb669d1dee = L.marker(
                [37.79471, -122.40195],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_541e263b5f1149d372654343a9631d21 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3e633487a2ca2aab00a62cdb669d1dee.setIcon(icon_541e263b5f1149d372654343a9631d21);
        
    
            marker_3e633487a2ca2aab00a62cdb669d1dee.bindTooltip(
                `<div>
                     STARTUPS: Netbooks
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_85c305d30d2a87c43d16d790d3c57509 = L.marker(
                [37.776878, -122.400934],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4e5e2128022a24a9b591c7950ce0da91 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_85c305d30d2a87c43d16d790d3c57509.setIcon(icon_4e5e2128022a24a9b591c7950ce0da91);
        
    
            marker_85c305d30d2a87c43d16d790d3c57509.bindTooltip(
                `<div>
                     STARTUPS: SlideShare
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1f1d025b9463d1545ad0477d86605a94 = L.marker(
                [37.76785, -122.392839],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_752853aa5bf4d60e492615f88afe5ad2 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1f1d025b9463d1545ad0477d86605a94.setIcon(icon_752853aa5bf4d60e492615f88afe5ad2);
        
    
            marker_1f1d025b9463d1545ad0477d86605a94.bindTooltip(
                `<div>
                     STARTUPS: Wikimedia Foundation
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_bbe09ab2acf72e21dda7194b1736e853 = L.marker(
                [37.7617955, -122.4092704],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a95ea870b4112d7887d95d0e07b3a04b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_bbe09ab2acf72e21dda7194b1736e853.setIcon(icon_a95ea870b4112d7887d95d0e07b3a04b);
        
    
            marker_bbe09ab2acf72e21dda7194b1736e853.bindTooltip(
                `<div>
                     STARTUPS: Xoopit
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_45001d5a8d4145476b362bab89088f76 = L.marker(
                [37.773688, -122.423468],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_0b13d400c77a68b78d02fd7c6e8dabe0 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_45001d5a8d4145476b362bab89088f76.setIcon(icon_0b13d400c77a68b78d02fd7c6e8dabe0);
        
    
            marker_45001d5a8d4145476b362bab89088f76.bindTooltip(
                `<div>
                     STARTUPS: Placecast
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1aadeb8f4416b3a2786dc5882c3feac7 = L.marker(
                [37.787646, -122.402759],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8f564f0cc78a40f12461431ffeecdaeb = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1aadeb8f4416b3a2786dc5882c3feac7.setIcon(icon_8f564f0cc78a40f12461431ffeecdaeb);
        
    
            marker_1aadeb8f4416b3a2786dc5882c3feac7.bindTooltip(
                `<div>
                     STARTUPS: Stylehive
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a31a53398400bd8a3b3ddc74af97cacb = L.marker(
                [37.792949, -122.398099],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_16d9e00f95287bc2e1a83e6e1a72c344 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a31a53398400bd8a3b3ddc74af97cacb.setIcon(icon_16d9e00f95287bc2e1a83e6e1a72c344);
        
    
            marker_a31a53398400bd8a3b3ddc74af97cacb.bindTooltip(
                `<div>
                     STARTUPS: Undertone
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_19f9850bf60e7584efdc2d719560b642 = L.marker(
                [37.792083, -122.3946404],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e861da0bb32bef009060e06ec5c81b65 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_19f9850bf60e7584efdc2d719560b642.setIcon(icon_e861da0bb32bef009060e06ec5c81b65);
        
    
            marker_19f9850bf60e7584efdc2d719560b642.bindTooltip(
                `<div>
                     STARTUPS: Marin Software
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_ed9159e8bbdaecfa8c84a4e59dffafc9 = L.marker(
                [37.78236, -122.403256],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_90ab519ab1bec2c17c9eeface17f3050 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_ed9159e8bbdaecfa8c84a4e59dffafc9.setIcon(icon_90ab519ab1bec2c17c9eeface17f3050);
        
    
            marker_ed9159e8bbdaecfa8c84a4e59dffafc9.bindTooltip(
                `<div>
                     STARTUPS: Huddle
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_86e3c6ed209a6a2ae6b5744e3f2cafa2 = L.marker(
                [37.800209, -122.442592],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_6d48c6be67cfd973605bda9d834d19bd = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_86e3c6ed209a6a2ae6b5744e3f2cafa2.setIcon(icon_6d48c6be67cfd973605bda9d834d19bd);
        
    
            marker_86e3c6ed209a6a2ae6b5744e3f2cafa2.bindTooltip(
                `<div>
                     STARTUPS: Boxbe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8f0f0e46b9698b7162a1c1ede046f60f = L.marker(
                [37.7938969, -122.4000965],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_36f6ca8404bb1b6721e3018a1bb9f69a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8f0f0e46b9698b7162a1c1ede046f60f.setIcon(icon_36f6ca8404bb1b6721e3018a1bb9f69a);
        
    
            marker_8f0f0e46b9698b7162a1c1ede046f60f.bindTooltip(
                `<div>
                     STARTUPS: RichRelevance
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_94e603c14c19b3d8e2dc2de0c133ccf7 = L.marker(
                [37.789551, -122.40036],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8a3ea93f57998787bcee920a64d40b97 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_94e603c14c19b3d8e2dc2de0c133ccf7.setIcon(icon_8a3ea93f57998787bcee920a64d40b97);
        
    
            marker_94e603c14c19b3d8e2dc2de0c133ccf7.bindTooltip(
                `<div>
                     STARTUPS: SquareTrade
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_56b703a130a5c993fbf6d9c7a4587025 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_03c15bb94fd41bd6728e8743ffb3e4da = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_56b703a130a5c993fbf6d9c7a4587025.setIcon(icon_03c15bb94fd41bd6728e8743ffb3e4da);
        
    
            marker_56b703a130a5c993fbf6d9c7a4587025.bindTooltip(
                `<div>
                     STARTUPS: GitHub
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3c855e8a86253517459487d314aaa5a8 = L.marker(
                [37.7819286, -122.4041764],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_852e5d92c1c4d0b1dd9676e5459c584f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3c855e8a86253517459487d314aaa5a8.setIcon(icon_852e5d92c1c4d0b1dd9676e5459c584f);
        
    
            marker_3c855e8a86253517459487d314aaa5a8.bindTooltip(
                `<div>
                     STARTUPS: Globant
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1baf4dc4079114ed5388f4d00a9f6858 = L.marker(
                [37.566042, -122.322954],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d1fb442e3694b452bdc400c2950e5e7f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1baf4dc4079114ed5388f4d00a9f6858.setIcon(icon_d1fb442e3694b452bdc400c2950e5e7f);
        
    
            marker_1baf4dc4079114ed5388f4d00a9f6858.bindTooltip(
                `<div>
                     STARTUPS: Apture
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_7ebf004892e428719128bf097f9eda6c = L.marker(
                [37.4465496, -122.1709208],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cd4f7250119f08913f10fe402b0ccaf8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_7ebf004892e428719128bf097f9eda6c.setIcon(icon_cd4f7250119f08913f10fe402b0ccaf8);
        
    
            marker_7ebf004892e428719128bf097f9eda6c.bindTooltip(
                `<div>
                     STARTUPS: InMobi
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a4301fbcd69e1dcc10383af4521f1ead = L.marker(
                [37.748676, -122.415813],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_42b8e55a03994032da89f306f4e2bc5c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a4301fbcd69e1dcc10383af4521f1ead.setIcon(icon_42b8e55a03994032da89f306f4e2bc5c);
        
    
            marker_a4301fbcd69e1dcc10383af4521f1ead.bindTooltip(
                `<div>
                     STARTUPS: Aardvark
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_198c96b00daaf95648c792824e297731 = L.marker(
                [37.775058, -122.419966],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e83f7f9a72ea2f5570ac26ea0b8aeffc = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_198c96b00daaf95648c792824e297731.setIcon(icon_e83f7f9a72ea2f5570ac26ea0b8aeffc);
        
    
            marker_198c96b00daaf95648c792824e297731.bindTooltip(
                `<div>
                     STARTUPS: SQLstream
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9e9d65aefccce5681b76ba85a7952f93 = L.marker(
                [37.771208, -122.418129],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_292188905a98655c61aac86d9d3b734d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9e9d65aefccce5681b76ba85a7952f93.setIcon(icon_292188905a98655c61aac86d9d3b734d);
        
    
            marker_9e9d65aefccce5681b76ba85a7952f93.bindTooltip(
                `<div>
                     STARTUPS: Triggit
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_ec9e0c26ca39799bc167de1e7986fcb3 = L.marker(
                [37.7893215, -122.4013624],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4a6e822822795f497696f08f105b8472 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_ec9e0c26ca39799bc167de1e7986fcb3.setIcon(icon_4a6e822822795f497696f08f105b8472);
        
    
            marker_ec9e0c26ca39799bc167de1e7986fcb3.bindTooltip(
                `<div>
                     STARTUPS: Serious Business
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_6c20c1717bec2d3f6e97621db5922221 = L.marker(
                [37.755012, -122.418137],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e21a4c1c90d18cd9452ad7f0cd4ff0f6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_6c20c1717bec2d3f6e97621db5922221.setIcon(icon_e21a4c1c90d18cd9452ad7f0cd4ff0f6);
        
    
            marker_6c20c1717bec2d3f6e97621db5922221.bindTooltip(
                `<div>
                     STARTUPS: RateItAll
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4db07deab19ebf527a099ed74cab9b89 = L.marker(
                [37.791242, -122.397133],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ce676ea5d555777c80ac541b8f8185cf = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4db07deab19ebf527a099ed74cab9b89.setIcon(icon_ce676ea5d555777c80ac541b8f8185cf);
        
    
            marker_4db07deab19ebf527a099ed74cab9b89.bindTooltip(
                `<div>
                     STARTUPS: Tealeaf
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8b48244fd8a659efa1cdf8bea8ac2c3f = L.marker(
                [37.3944561, -122.0785314],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7b04003f9938614d843528ff844cfb73 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8b48244fd8a659efa1cdf8bea8ac2c3f.setIcon(icon_7b04003f9938614d843528ff844cfb73);
        
    
            marker_8b48244fd8a659efa1cdf8bea8ac2c3f.bindTooltip(
                `<div>
                     STARTUPS: Kabam
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a70be47a514118cd328aad0c60c9b910 = L.marker(
                [37.269175, -119.306607],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_60ab79ac7620861ebd883433b67b1aa9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a70be47a514118cd328aad0c60c9b910.setIcon(icon_60ab79ac7620861ebd883433b67b1aa9);
        
    
            marker_a70be47a514118cd328aad0c60c9b910.bindTooltip(
                `<div>
                     STARTUPS: New Relic
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9da342c194b7a389a66c0ec6c1b5aa12 = L.marker(
                [40.234451, -111.658656],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c702729058f06b614c4d94c3d38f7e5e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9da342c194b7a389a66c0ec6c1b5aa12.setIcon(icon_c702729058f06b614c4d94c3d38f7e5e);
        
    
            marker_9da342c194b7a389a66c0ec6c1b5aa12.bindTooltip(
                `<div>
                     STARTUPS: Zinch
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_99feeeb824fccd0ba2362f8e5a1d63a3 = L.marker(
                [37.787204, -122.397884],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9264c1d05b42bd4f82fa613077e871e8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_99feeeb824fccd0ba2362f8e5a1d63a3.setIcon(icon_9264c1d05b42bd4f82fa613077e871e8);
        
    
            marker_99feeeb824fccd0ba2362f8e5a1d63a3.bindTooltip(
                `<div>
                     STARTUPS: Yoono
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5116dde8e4a546153574708d08387fce = L.marker(
                [37.777513, -122.397044],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_148dbd4e8f9c87434915869c0ec85748 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5116dde8e4a546153574708d08387fce.setIcon(icon_148dbd4e8f9c87434915869c0ec85748);
        
    
            marker_5116dde8e4a546153574708d08387fce.bindTooltip(
                `<div>
                     STARTUPS: Citizen Sports
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_6ab9940086981986f412c1f274a454e7 = L.marker(
                [37.7749295, -122.4194155],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_974bdbcbb49e63ffb24dfdc3c6bd10b6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_6ab9940086981986f412c1f274a454e7.setIcon(icon_974bdbcbb49e63ffb24dfdc3c6bd10b6);
        
    
            marker_6ab9940086981986f412c1f274a454e7.bindTooltip(
                `<div>
                     STARTUPS: EatLime
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e923f5390bfc0c98eee12582e6143231 = L.marker(
                [37.7654195, -122.4119381],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5cfe5adbca27cc7771af2f0e17f7f6a9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e923f5390bfc0c98eee12582e6143231.setIcon(icon_5cfe5adbca27cc7771af2f0e17f7f6a9);
        
    
            marker_e923f5390bfc0c98eee12582e6143231.bindTooltip(
                `<div>
                     STARTUPS: TuneUp
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e337276be81bc73cc30e96b6c8ec5c49 = L.marker(
                [37.789518, -122.394265],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_385fd27d25b434894639eaa552ca420c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e337276be81bc73cc30e96b6c8ec5c49.setIcon(icon_385fd27d25b434894639eaa552ca420c);
        
    
            marker_e337276be81bc73cc30e96b6c8ec5c49.bindTooltip(
                `<div>
                     STARTUPS: Wavemaker Software
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_135203e73d9709814f73da2203506067 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_1f4fff33e93af9e23c8564587c51d328 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_135203e73d9709814f73da2203506067.setIcon(icon_1f4fff33e93af9e23c8564587c51d328);
        
    
            marker_135203e73d9709814f73da2203506067.bindTooltip(
                `<div>
                     STARTUPS: Stitcher
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_0cb92d4bc79695bfd0a16145cacf6deb = L.marker(
                [37.787445, -122.397847],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_0acf22dfaaeebb58557cd7a1e80e9932 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_0cb92d4bc79695bfd0a16145cacf6deb.setIcon(icon_0acf22dfaaeebb58557cd7a1e80e9932);
        
    
            marker_0cb92d4bc79695bfd0a16145cacf6deb.bindTooltip(
                `<div>
                     STARTUPS: TerraPass
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_dd37ebc1298de67847d075f2757d3ff2 = L.marker(
                [37.7726402, -122.4099154],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_92caf13a1323ae2982c2d4fb6a83c94d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_dd37ebc1298de67847d075f2757d3ff2.setIcon(icon_92caf13a1323ae2982c2d4fb6a83c94d);
        
    
            marker_dd37ebc1298de67847d075f2757d3ff2.bindTooltip(
                `<div>
                     STARTUPS: Zoosk
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5927c216d9f57e2922f3a4630b744ea3 = L.marker(
                [37.785792, -122.405081],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3c35b099e026e415c9f5b4cd57b58eed = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5927c216d9f57e2922f3a4630b744ea3.setIcon(icon_3c35b099e026e415c9f5b4cd57b58eed);
        
    
            marker_5927c216d9f57e2922f3a4630b744ea3.bindTooltip(
                `<div>
                     STARTUPS: Transpera
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e92ac383087e8715e5cb08ef2df4ab46 = L.marker(
                [37.7911186, -122.4011706],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f8ae5eae2e9ca59968d2b7def898a5e7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e92ac383087e8715e5cb08ef2df4ab46.setIcon(icon_f8ae5eae2e9ca59968d2b7def898a5e7);
        
    
            marker_e92ac383087e8715e5cb08ef2df4ab46.bindTooltip(
                `<div>
                     STARTUPS: Bizo
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5f810893d5c7186c3f94ed955582143e = L.marker(
                [37.7915, -122.40045],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3e425b5bb5cae380cbb6bf1df7859ac0 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5f810893d5c7186c3f94ed955582143e.setIcon(icon_3e425b5bb5cae380cbb6bf1df7859ac0);
        
    
            marker_5f810893d5c7186c3f94ed955582143e.bindTooltip(
                `<div>
                     STARTUPS: Zinio
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c4d643eceefba2633208131a39922b57 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e90e36ba0b7a9ef72e78882cfb99a7eb = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c4d643eceefba2633208131a39922b57.setIcon(icon_e90e36ba0b7a9ef72e78882cfb99a7eb);
        
    
            marker_c4d643eceefba2633208131a39922b57.bindTooltip(
                `<div>
                     STARTUPS: PurePlay
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9da656b175e844f2f98a38031152aa4f = L.marker(
                [37.790346, -122.40185],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_23f48bf78503b2135a06fb5c4b8890b3 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9da656b175e844f2f98a38031152aa4f.setIcon(icon_23f48bf78503b2135a06fb5c4b8890b3);
        
    
            marker_9da656b175e844f2f98a38031152aa4f.bindTooltip(
                `<div>
                     STARTUPS: Aruspex
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_85ba3e1b7a936edd6129bd000a1edaca = L.marker(
                [37.779507, -122.39071],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b98fc4ed240b5b1e7f6c07cde83e8168 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_85ba3e1b7a936edd6129bd000a1edaca.setIcon(icon_b98fc4ed240b5b1e7f6c07cde83e8168);
        
    
            marker_85ba3e1b7a936edd6129bd000a1edaca.bindTooltip(
                `<div>
                     STARTUPS: Cloudmark
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a78ed050208a179c98e40a0c0712a273 = L.marker(
                [37.7886685, -122.4005576],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_04917d9dd4cbd9ffd6eb57cd33854ce7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a78ed050208a179c98e40a0c0712a273.setIcon(icon_04917d9dd4cbd9ffd6eb57cd33854ce7);
        
    
            marker_a78ed050208a179c98e40a0c0712a273.bindTooltip(
                `<div>
                     STARTUPS: TRUSTe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d9891241ba5740dd5831ed05180b0a65 = L.marker(
                [37.782101, -122.389519],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c27e4a122e0f3743308507235eb9c04d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d9891241ba5740dd5831ed05180b0a65.setIcon(icon_c27e4a122e0f3743308507235eb9c04d);
        
    
            marker_d9891241ba5740dd5831ed05180b0a65.bindTooltip(
                `<div>
                     STARTUPS: Jambool
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_87189fcfb129fce035dd44888d10b2b9 = L.marker(
                [37.796164, -122.420903],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_90ee53819e9f589522a4c362511939f7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_87189fcfb129fce035dd44888d10b2b9.setIcon(icon_90ee53819e9f589522a4c362511939f7);
        
    
            marker_87189fcfb129fce035dd44888d10b2b9.bindTooltip(
                `<div>
                     STARTUPS: StartForce
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a20bd8e06bcf762ff1ea722796ecf027 = L.marker(
                [37.7886717, -122.392725],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_dc02dee2120055d4e423f28c44ee83e6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a20bd8e06bcf762ff1ea722796ecf027.setIcon(icon_dc02dee2120055d4e423f28c44ee83e6);
        
    
            marker_a20bd8e06bcf762ff1ea722796ecf027.bindTooltip(
                `<div>
                     STARTUPS: Practice Fusion
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b2f08fed298b20c7374daa756ea9da31 = L.marker(
                [37.7819267, -122.3932389],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8e7b13e138fda65bb608d0cea42b6a64 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b2f08fed298b20c7374daa756ea9da31.setIcon(icon_8e7b13e138fda65bb608d0cea42b6a64);
        
    
            marker_b2f08fed298b20c7374daa756ea9da31.bindTooltip(
                `<div>
                     STARTUPS: Socialcast
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c00674a59819e7439e744a93e4e6d955 = L.marker(
                [37.766909, -122.406676],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b1e1158eb452ca111533c64c33687f6e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c00674a59819e7439e744a93e4e6d955.setIcon(icon_b1e1158eb452ca111533c64c33687f6e);
        
    
            marker_c00674a59819e7439e744a93e4e6d955.bindTooltip(
                `<div>
                     STARTUPS: Razz
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c423e01bdb4c94809f7a6c5c25107c6f = L.marker(
                [37.764169, -122.402021],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2f5fd7c921a5a6c11b6b0376d12a4542 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c423e01bdb4c94809f7a6c5c25107c6f.setIcon(icon_2f5fd7c921a5a6c11b6b0376d12a4542);
        
    
            marker_c423e01bdb4c94809f7a6c5c25107c6f.bindTooltip(
                `<div>
                     STARTUPS: InsideView
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e69aa73b6373f16fecb1e7f65f6c70f9 = L.marker(
                [37.7932747, -122.399929],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f8acc85121429a2b8b8e06388686a0b7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e69aa73b6373f16fecb1e7f65f6c70f9.setIcon(icon_f8acc85121429a2b8b8e06388686a0b7);
        
    
            marker_e69aa73b6373f16fecb1e7f65f6c70f9.bindTooltip(
                `<div>
                     STARTUPS: Clickability
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_72433a0109f5d3580221115a0db0d03a = L.marker(
                [37.7895289, -122.3885899],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2814570b4ab3f818f2869c578ef1f820 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_72433a0109f5d3580221115a0db0d03a.setIcon(icon_2814570b4ab3f818f2869c578ef1f820);
        
    
            marker_72433a0109f5d3580221115a0db0d03a.bindTooltip(
                `<div>
                     STARTUPS: WideOrbit
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d1e607c3a32acfff0ad9fdbe8badddd1 = L.marker(
                [37.787646, -122.402759],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f38f17ddcb3e0dda58ae303b440f0359 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d1e607c3a32acfff0ad9fdbe8badddd1.setIcon(icon_f38f17ddcb3e0dda58ae303b440f0359);
        
    
            marker_d1e607c3a32acfff0ad9fdbe8badddd1.bindTooltip(
                `<div>
                     STARTUPS: Keibi Technologies
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_33ac0f10444b9815ffd925fbf8ff8acc = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_13a87e79fa14827e33758c007cd47f70 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_33ac0f10444b9815ffd925fbf8ff8acc.setIcon(icon_13a87e79fa14827e33758c007cd47f70);
        
    
            marker_33ac0f10444b9815ffd925fbf8ff8acc.bindTooltip(
                `<div>
                     STARTUPS: Posterous
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a70b1d392472eb3544efbcdd4e78477c = L.marker(
                [37.758352, -122.398379],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_597d41153585a66c9817192c1b446fc1 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a70b1d392472eb3544efbcdd4e78477c.setIcon(icon_597d41153585a66c9817192c1b446fc1);
        
    
            marker_a70b1d392472eb3544efbcdd4e78477c.bindTooltip(
                `<div>
                     STARTUPS: Open Places
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_87c9483e61c2c35e6001fe4cff025659 = L.marker(
                [37.789902, -122.4007791],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_070068918d19bf6760688c1235100860 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_87c9483e61c2c35e6001fe4cff025659.setIcon(icon_070068918d19bf6760688c1235100860);
        
    
            marker_87c9483e61c2c35e6001fe4cff025659.bindTooltip(
                `<div>
                     STARTUPS: Genbook
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_29a02631c8dcfa765b911b5f8489c9f2 = L.marker(
                [37.7710177, -122.4025976],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3764df561948205c2ad3b05f433ee8ca = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_29a02631c8dcfa765b911b5f8489c9f2.setIcon(icon_3764df561948205c2ad3b05f433ee8ca);
        
    
            marker_29a02631c8dcfa765b911b5f8489c9f2.bindTooltip(
                `<div>
                     STARTUPS: Flowgram
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f98b6744c05f7954cd4c62d6fb8bec20 = L.marker(
                [43.637296, -79.425455],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_57b314ecce0c2a57b93d98302cc1e683 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f98b6744c05f7954cd4c62d6fb8bec20.setIcon(icon_57b314ecce0c2a57b93d98302cc1e683);
        
    
            marker_f98b6744c05f7954cd4c62d6fb8bec20.bindTooltip(
                `<div>
                     STARTUPS: Achievers
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_62dc8124a71de8a00359dbdcc8506e40 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_925575bf2fa4c3e1d6d1cbb16e6ca04d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_62dc8124a71de8a00359dbdcc8506e40.setIcon(icon_925575bf2fa4c3e1d6d1cbb16e6ca04d);
        
    
            marker_62dc8124a71de8a00359dbdcc8506e40.bindTooltip(
                `<div>
                     STARTUPS: Medialets
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_ae9649c03dbe8d5808836dcda0f94331 = L.marker(
                [37.788653, -122.401333],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_10db5e7cd3f1ddd7f7c1e5b8c342b30b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_ae9649c03dbe8d5808836dcda0f94331.setIcon(icon_10db5e7cd3f1ddd7f7c1e5b8c342b30b);
        
    
            marker_ae9649c03dbe8d5808836dcda0f94331.bindTooltip(
                `<div>
                     STARTUPS: Involver
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2a29f2c8da182b6091440eef922fcace = L.marker(
                [37.7850326, -122.4004738],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a7f3dc5e0199b18488a97c38cc302616 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2a29f2c8da182b6091440eef922fcace.setIcon(icon_a7f3dc5e0199b18488a97c38cc302616);
        
    
            marker_2a29f2c8da182b6091440eef922fcace.bindTooltip(
                `<div>
                     STARTUPS: ON24
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e2a0f13d1067eb2d5c088a20e8b551c7 = L.marker(
                [55.676294, 12.568116],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_1dfed75a8a959dbb61626fbb9b9915a9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e2a0f13d1067eb2d5c088a20e8b551c7.setIcon(icon_1dfed75a8a959dbb61626fbb9b9915a9);
        
    
            marker_e2a0f13d1067eb2d5c088a20e8b551c7.bindTooltip(
                `<div>
                     STARTUPS: Zendesk
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d307ce2a8f9d1b46e823016bb0f6234e = L.marker(
                [37.782085, -122.409747],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c32096591e72d6d428ed113f9c889dd9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d307ce2a8f9d1b46e823016bb0f6234e.setIcon(icon_c32096591e72d6d428ed113f9c889dd9);
        
    
            marker_d307ce2a8f9d1b46e823016bb0f6234e.bindTooltip(
                `<div>
                     STARTUPS: Streetline
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_7db0888a4ff440498cb6cec4571da63c = L.marker(
                [37.8059887, -122.4099154],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d3122649903483146725a2555e50ad1d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_7db0888a4ff440498cb6cec4571da63c.setIcon(icon_d3122649903483146725a2555e50ad1d);
        
    
            marker_7db0888a4ff440498cb6cec4571da63c.bindTooltip(
                `<div>
                     STARTUPS: trueAnthem
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3d13e7202270ccf80476b705043f1ccb = L.marker(
                [37.786906, -122.397672],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7be29e510aa9cfb8f6954bad000d0a7a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3d13e7202270ccf80476b705043f1ccb.setIcon(icon_7be29e510aa9cfb8f6954bad000d0a7a);
        
    
            marker_3d13e7202270ccf80476b705043f1ccb.bindTooltip(
                `<div>
                     STARTUPS: Lexy
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_fcb46b432ac048d66908059bda569aa9 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ab99249d4114042349823a2f60c0ae5a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_fcb46b432ac048d66908059bda569aa9.setIcon(icon_ab99249d4114042349823a2f60c0ae5a);
        
    
            marker_fcb46b432ac048d66908059bda569aa9.bindTooltip(
                `<div>
                     STARTUPS: Hands-On Mobile
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4d2074b6a604217a1aac64c5fb7031b7 = L.marker(
                [37.805289, -122.404448],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3dba19df0e659dfba9f82b837c106ea1 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4d2074b6a604217a1aac64c5fb7031b7.setIcon(icon_3dba19df0e659dfba9f82b837c106ea1);
        
    
            marker_4d2074b6a604217a1aac64c5fb7031b7.bindTooltip(
                `<div>
                     STARTUPS: Carbonetworks
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_00bbe4fd2c24215c5d256409cd4cf418 = L.marker(
                [37.797435, -122.403175],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9c4ae9d19a2c0804815205353d8b664f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_00bbe4fd2c24215c5d256409cd4cf418.setIcon(icon_9c4ae9d19a2c0804815205353d8b664f);
        
    
            marker_00bbe4fd2c24215c5d256409cd4cf418.bindTooltip(
                `<div>
                     STARTUPS: Minted
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1a90fae43f15545c9dea82b474927d7a = L.marker(
                [37.5552776, -122.2997532],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_607e73a76de88e29f75590dc122ef54d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1a90fae43f15545c9dea82b474927d7a.setIcon(icon_607e73a76de88e29f75590dc122ef54d);
        
    
            marker_1a90fae43f15545c9dea82b474927d7a.bindTooltip(
                `<div>
                     STARTUPS: Appirio
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2a1bcce4a293f850d3eb7453c07ff0c5 = L.marker(
                [37.7797255, -122.4020593],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_fce9020294aa545c36991e3b87347590 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2a1bcce4a293f850d3eb7453c07ff0c5.setIcon(icon_fce9020294aa545c36991e3b87347590);
        
    
            marker_2a1bcce4a293f850d3eb7453c07ff0c5.bindTooltip(
                `<div>
                     STARTUPS: Kontagent
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_7530f39cc41d9a6c43f3dfd251e31989 = L.marker(
                [37.791068, -122.401942],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d24ad45114957540b467aeb266302acb = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_7530f39cc41d9a6c43f3dfd251e31989.setIcon(icon_d24ad45114957540b467aeb266302acb);
        
    
            marker_7530f39cc41d9a6c43f3dfd251e31989.bindTooltip(
                `<div>
                     STARTUPS: Allvoices
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_31cfdac54de6e059c064ccd0455b6621 = L.marker(
                [37.7893215, -122.4013624],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_91e8e0da7bbee0a3c926ec7b80f75127 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_31cfdac54de6e059c064ccd0455b6621.setIcon(icon_91e8e0da7bbee0a3c926ec7b80f75127);
        
    
            marker_31cfdac54de6e059c064ccd0455b6621.bindTooltip(
                `<div>
                     STARTUPS: Huddler
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b311a7533f420f31353977e03798ad24 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_945c52e91315a41146a714c0b9199e05 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b311a7533f420f31353977e03798ad24.setIcon(icon_945c52e91315a41146a714c0b9199e05);
        
    
            marker_b311a7533f420f31353977e03798ad24.bindTooltip(
                `<div>
                     STARTUPS: Zorap
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_838ff0ce3fae9a3e4af06305c56a7d6b = L.marker(
                [37.7705096, -122.4015807],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cc0cb23c5a92524b563903a6f3dc3bde = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_838ff0ce3fae9a3e4af06305c56a7d6b.setIcon(icon_cc0cb23c5a92524b563903a6f3dc3bde);
        
    
            marker_838ff0ce3fae9a3e4af06305c56a7d6b.bindTooltip(
                `<div>
                     STARTUPS: ngmoco
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8d159af002d0813576566ace81e285ff = L.marker(
                [37.7976785, -122.4018163],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_271d8e5122df8334c5ecf0ddfb7ab41d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8d159af002d0813576566ace81e285ff.setIcon(icon_271d8e5122df8334c5ecf0ddfb7ab41d);
        
    
            marker_8d159af002d0813576566ace81e285ff.bindTooltip(
                `<div>
                     STARTUPS: Sharethrough
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_43eb18dabbbf7b7e9458c0e7b632ef40 = L.marker(
                [37.798684, -122.40264],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_adbd022307192ba9592022261d1f7d6f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_43eb18dabbbf7b7e9458c0e7b632ef40.setIcon(icon_adbd022307192ba9592022261d1f7d6f);
        
    
            marker_43eb18dabbbf7b7e9458c0e7b632ef40.bindTooltip(
                `<div>
                     STARTUPS: Paymo
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_be28ac5865ee6da44a23446c705f51f0 = L.marker(
                [37.796903, -122.404904],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8075e646f38cbe5babf802a80aef1449 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_be28ac5865ee6da44a23446c705f51f0.setIcon(icon_8075e646f38cbe5babf802a80aef1449);
        
    
            marker_be28ac5865ee6da44a23446c705f51f0.bindTooltip(
                `<div>
                     STARTUPS: Recommind
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f46a3681480f325101d92c203a8a3e30 = L.marker(
                [37.787722, -122.3993979],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_065899e1548063a4ae95a1d1ae958f10 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f46a3681480f325101d92c203a8a3e30.setIcon(icon_065899e1548063a4ae95a1d1ae958f10);
        
    
            marker_f46a3681480f325101d92c203a8a3e30.bindTooltip(
                `<div>
                     STARTUPS: Innotas
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a5757f522e1c5cce90539490043ec3ce = L.marker(
                [37.7899027, -122.4007846],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2970622a1d9cad277b13ee3f422cf2f8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a5757f522e1c5cce90539490043ec3ce.setIcon(icon_2970622a1d9cad277b13ee3f422cf2f8);
        
    
            marker_a5757f522e1c5cce90539490043ec3ce.bindTooltip(
                `<div>
                     STARTUPS: Lyft
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e39f997acf355a979fe8980d2203dcd2 = L.marker(
                [37.796843, -122.400346],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_043683b990b6205ff21230e9e8c17d8a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e39f997acf355a979fe8980d2203dcd2.setIcon(icon_043683b990b6205ff21230e9e8c17d8a);
        
    
            marker_e39f997acf355a979fe8980d2203dcd2.bindTooltip(
                `<div>
                     STARTUPS: Demandbase
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2e4a83403ce03cbe33c813c75b149bbe = L.marker(
                [37.776246, -122.4179223],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_016cd42b8abe51b1b97cdc9d0da7f4a7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2e4a83403ce03cbe33c813c75b149bbe.setIcon(icon_016cd42b8abe51b1b97cdc9d0da7f4a7);
        
    
            marker_2e4a83403ce03cbe33c813c75b149bbe.bindTooltip(
                `<div>
                     STARTUPS: Yammer
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_bfbaf1f3849fab4c1cf4d211e807d2f8 = L.marker(
                [37.7983181, -122.4000032],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_709ccfb378286ecfd8dde91726ffcec1 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_bfbaf1f3849fab4c1cf4d211e807d2f8.setIcon(icon_709ccfb378286ecfd8dde91726ffcec1);
        
    
            marker_bfbaf1f3849fab4c1cf4d211e807d2f8.bindTooltip(
                `<div>
                     STARTUPS: GoodGuide
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_287bece2f3f62a59e5206e27b45a9ae8 = L.marker(
                [37.580093, -122.347745],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_03df087ae2aa1f38b4d87e591b479055 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_287bece2f3f62a59e5206e27b45a9ae8.setIcon(icon_03df087ae2aa1f38b4d87e591b479055);
        
    
            marker_287bece2f3f62a59e5206e27b45a9ae8.bindTooltip(
                `<div>
                     STARTUPS: ConnectSolutions
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9258cf006cece31471c4c5608bfd65ce = L.marker(
                [37.7870923, -122.3999719],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_0471a860c2b2646aff08fb219a2ce776 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9258cf006cece31471c4c5608bfd65ce.setIcon(icon_0471a860c2b2646aff08fb219a2ce776);
        
    
            marker_9258cf006cece31471c4c5608bfd65ce.bindTooltip(
                `<div>
                     STARTUPS: Lolapps
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f1e03a200e02e075f552200427566135 = L.marker(
                [37.783171, -122.392901],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cfb79d20b6251dce39896a5918021de6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f1e03a200e02e075f552200427566135.setIcon(icon_cfb79d20b6251dce39896a5918021de6);
        
    
            marker_f1e03a200e02e075f552200427566135.bindTooltip(
                `<div>
                     STARTUPS: Penguin Computing
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_41129cb085bf9d605c2983427819330b = L.marker(
                [37.764395, -122.401024],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c5bd361d0354c9d9753bf53ae3332ee7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_41129cb085bf9d605c2983427819330b.setIcon(icon_c5bd361d0354c9d9753bf53ae3332ee7);
        
    
            marker_41129cb085bf9d605c2983427819330b.bindTooltip(
                `<div>
                     STARTUPS: Pixelpipe
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b42bc48317d52fd8744acdae9131eabc = L.marker(
                [37.804619, -122.371203],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e9577b0278cb46c49fa531cc54bf3d2b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b42bc48317d52fd8744acdae9131eabc.setIcon(icon_e9577b0278cb46c49fa531cc54bf3d2b);
        
    
            marker_b42bc48317d52fd8744acdae9131eabc.bindTooltip(
                `<div>
                     STARTUPS: Brightstorm
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_47d522de492e916a2f27f4c13a924260 = L.marker(
                [37.79402, -122.4031242],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e5e314bef99291cb9b5963acf7af6968 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_47d522de492e916a2f27f4c13a924260.setIcon(icon_e5e314bef99291cb9b5963acf7af6968);
        
    
            marker_47d522de492e916a2f27f4c13a924260.bindTooltip(
                `<div>
                     STARTUPS: PicApp
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e0b68705658c4ac2d6605e1f1f084eaa = L.marker(
                [37.779624, -122.391236],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_96ca98b5a8ea8935a8e0a5b0ac8251d7 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e0b68705658c4ac2d6605e1f1f084eaa.setIcon(icon_96ca98b5a8ea8935a8e0a5b0ac8251d7);
        
    
            marker_e0b68705658c4ac2d6605e1f1f084eaa.bindTooltip(
                `<div>
                     STARTUPS: Smule
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c2a9d66d557ea6319aa7290332ea5300 = L.marker(
                [37.759117, -122.415869],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9de4a864198b5098ebcd5dc7dfc195d8 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c2a9d66d557ea6319aa7290332ea5300.setIcon(icon_9de4a864198b5098ebcd5dc7dfc195d8);
        
    
            marker_c2a9d66d557ea6319aa7290332ea5300.bindTooltip(
                `<div>
                     STARTUPS: OQO
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_41313b64899f62a7af9fdfe0ada808d0 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f69f918c2e6121f513cd268f912ac3ae = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_41313b64899f62a7af9fdfe0ada808d0.setIcon(icon_f69f918c2e6121f513cd268f912ac3ae);
        
    
            marker_41313b64899f62a7af9fdfe0ada808d0.bindTooltip(
                `<div>
                     STARTUPS: One Season
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cbf761e70a73a469d581b26c5a1e37f9 = L.marker(
                [50.095312, 9.049981],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_d94cf06dccdc09124885828e243bb175 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cbf761e70a73a469d581b26c5a1e37f9.setIcon(icon_d94cf06dccdc09124885828e243bb175);
        
    
            marker_cbf761e70a73a469d581b26c5a1e37f9.bindTooltip(
                `<div>
                     STARTUPS: Mindjet
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_87ee22e95594a170da1836d84068e4ac = L.marker(
                [37.782163, -122.400591],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_535eaba0c54709c39929004e6036f01a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_87ee22e95594a170da1836d84068e4ac.setIcon(icon_535eaba0c54709c39929004e6036f01a);
        
    
            marker_87ee22e95594a170da1836d84068e4ac.bindTooltip(
                `<div>
                     STARTUPS: Reframe It
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9fc133ea7ab2d40b905dc5f71f684073 = L.marker(
                [37.763293, -122.410752],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_50f081b4c478d559a6922e23cf7ad4c9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9fc133ea7ab2d40b905dc5f71f684073.setIcon(icon_50f081b4c478d559a6922e23cf7ad4c9);
        
    
            marker_9fc133ea7ab2d40b905dc5f71f684073.bindTooltip(
                `<div>
                     STARTUPS: Vinfolio
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_80917c282d8627060cf98a379db2642b = L.marker(
                [37.420728, -122.2103414],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3f4d15e4bf053d24f22f72315f6b8c45 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_80917c282d8627060cf98a379db2642b.setIcon(icon_3f4d15e4bf053d24f22f72315f6b8c45);
        
    
            marker_80917c282d8627060cf98a379db2642b.bindTooltip(
                `<div>
                     STARTUPS: Booyah
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_81404833185c59849a67b2a1206fdd9b = L.marker(
                [39.9052263, -86.0547016],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4c3608d2670a54855baa395b36548ede = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_81404833185c59849a67b2a1206fdd9b.setIcon(icon_4c3608d2670a54855baa395b36548ede);
        
    
            marker_81404833185c59849a67b2a1206fdd9b.bindTooltip(
                `<div>
                     STARTUPS: Formspring
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cd3836339f6281a7bc3f3629f91bad3f = L.marker(
                [37.788115, -122.397141],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_93cb0a5a7b4bb02b1cd599a228984f8c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cd3836339f6281a7bc3f3629f91bad3f.setIcon(icon_93cb0a5a7b4bb02b1cd599a228984f8c);
        
    
            marker_cd3836339f6281a7bc3f3629f91bad3f.bindTooltip(
                `<div>
                     STARTUPS: DocVerse
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e8e820d55db9e9695aebd843a72d9f80 = L.marker(
                [37.79088, -122.392521],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8a5ad06aeb2b439cd91cafe886d4108c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e8e820d55db9e9695aebd843a72d9f80.setIcon(icon_8a5ad06aeb2b439cd91cafe886d4108c);
        
    
            marker_e8e820d55db9e9695aebd843a72d9f80.bindTooltip(
                `<div>
                     STARTUPS: Sapient
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1793bb0dea9a1e1a230d1da05758352d = L.marker(
                [37.7898499, -122.4006831],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_71f3304a33fefe7704ff28c131e41d02 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1793bb0dea9a1e1a230d1da05758352d.setIcon(icon_71f3304a33fefe7704ff28c131e41d02);
        
    
            marker_1793bb0dea9a1e1a230d1da05758352d.bindTooltip(
                `<div>
                     STARTUPS: Twilio
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b186607accfbcda146a0df5edfaa8843 = L.marker(
                [37.7759421, -122.3931139],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_986fc3215a93b6fbf367107ebfef6f58 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b186607accfbcda146a0df5edfaa8843.setIcon(icon_986fc3215a93b6fbf367107ebfef6f58);
        
    
            marker_b186607accfbcda146a0df5edfaa8843.bindTooltip(
                `<div>
                     STARTUPS: LoopNet
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_dbc5c192e4a508b7f4aecd3fbfca797b = L.marker(
                [41.238038, -96.183033],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_508c19c4adbdca709f23474a5b4aa672 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_dbc5c192e4a508b7f4aecd3fbfca797b.setIcon(icon_508c19c4adbdca709f23474a5b4aa672);
        
    
            marker_dbc5c192e4a508b7f4aecd3fbfca797b.bindTooltip(
                `<div>
                     STARTUPS: Sojern
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2358f455c86b251bfd316c2ac2f47a32 = L.marker(
                [37.7710402, -122.4026325],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_c168221b936db4308b6c1473682bd409 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2358f455c86b251bfd316c2ac2f47a32.setIcon(icon_c168221b936db4308b6c1473682bd409);
        
    
            marker_2358f455c86b251bfd316c2ac2f47a32.bindTooltip(
                `<div>
                     STARTUPS: Soma Networks
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9bbed00f52bd104a2098918224879ba3 = L.marker(
                [37.7877967, -122.3989376],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_eb45823eead671f8a27c1f85f1267e99 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9bbed00f52bd104a2098918224879ba3.setIcon(icon_eb45823eead671f8a27c1f85f1267e99);
        
    
            marker_9bbed00f52bd104a2098918224879ba3.bindTooltip(
                `<div>
                     STARTUPS: YieldBuild
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_205ded735c03ddc572db708f6655bfdc = L.marker(
                [37.770067, -122.407603],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_fca7256be4673bc1e6f5289177ea703e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_205ded735c03ddc572db708f6655bfdc.setIcon(icon_fca7256be4673bc1e6f5289177ea703e);
        
    
            marker_205ded735c03ddc572db708f6655bfdc.bindTooltip(
                `<div>
                     STARTUPS: Coveroo
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a97aa320d71d53ecc689f7ab7a71d36a = L.marker(
                [37.785655, -122.398393],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8f33e3cc9126d866a1e2270c7fcf4178 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a97aa320d71d53ecc689f7ab7a71d36a.setIcon(icon_8f33e3cc9126d866a1e2270c7fcf4178);
        
    
            marker_a97aa320d71d53ecc689f7ab7a71d36a.bindTooltip(
                `<div>
                     STARTUPS: SenSage
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2decd0ba706ca0382b433307a9fc1220 = L.marker(
                [37.765388, -122.403111],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_660440b857e95f7f3589704facb1bd32 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2decd0ba706ca0382b433307a9fc1220.setIcon(icon_660440b857e95f7f3589704facb1bd32);
        
    
            marker_2decd0ba706ca0382b433307a9fc1220.bindTooltip(
                `<div>
                     STARTUPS: Jaspersoft
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4d484d90df51bb0c5a466468ffb60fc5 = L.marker(
                [37.665469, -122.38222],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_539adf7c6070a7175f71161b10571a60 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4d484d90df51bb0c5a466468ffb60fc5.setIcon(icon_539adf7c6070a7175f71161b10571a60);
        
    
            marker_4d484d90df51bb0c5a466468ffb60fc5.bindTooltip(
                `<div>
                     STARTUPS: Envivio
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e3e8f993778b9f5d7b9fdd433f1500d6 = L.marker(
                [37.791564, -122.399981],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_16ad912dec84d34645bc4f0f54009056 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e3e8f993778b9f5d7b9fdd433f1500d6.setIcon(icon_16ad912dec84d34645bc4f0f54009056);
        
    
            marker_e3e8f993778b9f5d7b9fdd433f1500d6.bindTooltip(
                `<div>
                     STARTUPS: SeeSaw Networks
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_d5060f660834a824fcfa4723620cd712 = L.marker(
                [34.0497638, -118.2474287],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_8f6e1bc013dc137b8ac63d600529153b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_d5060f660834a824fcfa4723620cd712.setIcon(icon_8f6e1bc013dc137b8ac63d600529153b);
        
    
            marker_d5060f660834a824fcfa4723620cd712.bindTooltip(
                `<div>
                     STARTUPS: Klout
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b747ac49d36569dfd601c4c96626a9ee = L.marker(
                [37.793783, -122.401001],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b257ab2cea10cfdbb86ccf74a13852d5 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_b747ac49d36569dfd601c4c96626a9ee.setIcon(icon_b257ab2cea10cfdbb86ccf74a13852d5);
        
    
            marker_b747ac49d36569dfd601c4c96626a9ee.bindTooltip(
                `<div>
                     STARTUPS: Mocana
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_df8426d5e66c834143a8483b80c46577 = L.marker(
                [37.794643, -122.401144],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5f795fe55fc23f8a3f84cfb5185a301a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_df8426d5e66c834143a8483b80c46577.setIcon(icon_5f795fe55fc23f8a3f84cfb5185a301a);
        
    
            marker_df8426d5e66c834143a8483b80c46577.bindTooltip(
                `<div>
                     STARTUPS: LiveVox
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_79749b4641bed38b23629c025ba81192 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_255c2f9b75eb2ba262fba24cd7c00e1d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_79749b4641bed38b23629c025ba81192.setIcon(icon_255c2f9b75eb2ba262fba24cd7c00e1d);
        
    
            marker_79749b4641bed38b23629c025ba81192.bindTooltip(
                `<div>
                     STARTUPS: Fuego Nation
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_794c9431d7869d5cd5c998a6c0c3c7a4 = L.marker(
                [37.791402, -122.402844],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a31cd00f2763d034a34853a23efb860d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_794c9431d7869d5cd5c998a6c0c3c7a4.setIcon(icon_a31cd00f2763d034a34853a23efb860d);
        
    
            marker_794c9431d7869d5cd5c998a6c0c3c7a4.bindTooltip(
                `<div>
                     STARTUPS: Clarabridge
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f760040665e2c039f42ff4b1de8e21ad = L.marker(
                [37.7652755, -122.4204782],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2eb5dd5884f2dd6f4a4005255bb0415f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f760040665e2c039f42ff4b1de8e21ad.setIcon(icon_2eb5dd5884f2dd6f4a4005255bb0415f);
        
    
            marker_f760040665e2c039f42ff4b1de8e21ad.bindTooltip(
                `<div>
                     STARTUPS: CrowdFlower
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4d9cf834eb6a15bf5b9debf4caade6cd = L.marker(
                [37.7887135, -122.3927145],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9553b4ee54c86ebdff0c19d17b27de4e = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4d9cf834eb6a15bf5b9debf4caade6cd.setIcon(icon_9553b4ee54c86ebdff0c19d17b27de4e);
        
    
            marker_4d9cf834eb6a15bf5b9debf4caade6cd.bindTooltip(
                `<div>
                     STARTUPS: Double Fusion
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_bc8491c0545f061cc99116baa177fa77 = L.marker(
                [37.7900992, -122.4021988],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_0f7f5749a130fb5165c94b4ba414be7a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_bc8491c0545f061cc99116baa177fa77.setIcon(icon_0f7f5749a130fb5165c94b4ba414be7a);
        
    
            marker_bc8491c0545f061cc99116baa177fa77.bindTooltip(
                `<div>
                     STARTUPS: 5th Finger
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f0edbc0481600f8992176b46338a5b23 = L.marker(
                [37.789801, -122.395327],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_87419d3b302f4cea388ded1e0e69b1f1 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f0edbc0481600f8992176b46338a5b23.setIcon(icon_87419d3b302f4cea388ded1e0e69b1f1);
        
    
            marker_f0edbc0481600f8992176b46338a5b23.bindTooltip(
                `<div>
                     STARTUPS: SmartTurn
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c64f1c9dd6287b19da1cf9fe1e59b535 = L.marker(
                [37.7800788, -122.3943096],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ed62487db8cbaba36f36e8bc7fa94fe9 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c64f1c9dd6287b19da1cf9fe1e59b535.setIcon(icon_ed62487db8cbaba36f36e8bc7fa94fe9);
        
    
            marker_c64f1c9dd6287b19da1cf9fe1e59b535.bindTooltip(
                `<div>
                     STARTUPS: SpongeFish
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5a8f88dfc6e52137f9d18b28547fc991 = L.marker(
                [37.789551, -122.40036],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2ebba1ebda37bcf80a7dee264bca4078 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5a8f88dfc6e52137f9d18b28547fc991.setIcon(icon_2ebba1ebda37bcf80a7dee264bca4078);
        
    
            marker_5a8f88dfc6e52137f9d18b28547fc991.bindTooltip(
                `<div>
                     STARTUPS: NorthStar Systems International
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9d76f1ac1a752eb457564747dc6e418c = L.marker(
                [37.7864636, -122.3978187],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2a30ff4a04e5081cf565b79f98233687 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9d76f1ac1a752eb457564747dc6e418c.setIcon(icon_2a30ff4a04e5081cf565b79f98233687);
        
    
            marker_9d76f1ac1a752eb457564747dc6e418c.bindTooltip(
                `<div>
                     STARTUPS: Roundbox
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_31f0bd8a7a289d3a5a517c93afcb6364 = L.marker(
                [37.793949, -122.398062],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5d479d8d57eaa2c1a4378fe245109e06 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_31f0bd8a7a289d3a5a517c93afcb6364.setIcon(icon_5d479d8d57eaa2c1a4378fe245109e06);
        
    
            marker_31f0bd8a7a289d3a5a517c93afcb6364.bindTooltip(
                `<div>
                     STARTUPS: Focus
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f07134b73eda916cf1a82de36d75e354 = L.marker(
                [37.791128, -122.398126],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_5e7a20b3be01320bbd9aa823f51266c3 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f07134b73eda916cf1a82de36d75e354.setIcon(icon_5e7a20b3be01320bbd9aa823f51266c3);
        
    
            marker_f07134b73eda916cf1a82de36d75e354.bindTooltip(
                `<div>
                     STARTUPS: BrightTALK
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5aba63e9f15093d0488d70ae98e1d3e0 = L.marker(
                [37.7905538, -122.4041485],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_eacfe3898885b4ad2dcc4c8e4817e990 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5aba63e9f15093d0488d70ae98e1d3e0.setIcon(icon_eacfe3898885b4ad2dcc4c8e4817e990);
        
    
            marker_5aba63e9f15093d0488d70ae98e1d3e0.bindTooltip(
                `<div>
                     STARTUPS: Heyzap
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_bb46abf9204583d803008e94d9571ace = L.marker(
                [37.785271, -122.397582],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e5ad192e3450ba5aa090b561d18fa7d2 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_bb46abf9204583d803008e94d9571ace.setIcon(icon_e5ad192e3450ba5aa090b561d18fa7d2);
        
    
            marker_bb46abf9204583d803008e94d9571ace.bindTooltip(
                `<div>
                     STARTUPS: Meez
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_32bb5afdc739bc41b84caad9695f1fff = L.marker(
                [37.796396, -122.404869],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_970df77810dfe160ed9625af2599d4d3 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_32bb5afdc739bc41b84caad9695f1fff.setIcon(icon_970df77810dfe160ed9625af2599d4d3);
        
    
            marker_32bb5afdc739bc41b84caad9695f1fff.bindTooltip(
                `<div>
                     STARTUPS: Pulse Entertainment
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e3beeb4f6c579b7325eb7589f25b64f1 = L.marker(
                [37.785271, -122.397582],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_64beeb43bc61f15c8414bd4040416a7f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e3beeb4f6c579b7325eb7589f25b64f1.setIcon(icon_64beeb43bc61f15c8414bd4040416a7f);
        
    
            marker_e3beeb4f6c579b7325eb7589f25b64f1.bindTooltip(
                `<div>
                     STARTUPS: Donnorwood Media
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_47d58e990c00c157fcf040d32cf70a40 = L.marker(
                [37.789518, -122.394265],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_af53f4044e4a7b51beef453edd52752a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_47d58e990c00c157fcf040d32cf70a40.setIcon(icon_af53f4044e4a7b51beef453edd52752a);
        
    
            marker_47d58e990c00c157fcf040d32cf70a40.bindTooltip(
                `<div>
                     STARTUPS: LesConcierges
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_afe14dd77dac0d9c78e1776ce852fae2 = L.marker(
                [37.7800539, -122.3912492],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_cc53a2e28d13c36ed68dbb3b8f80681a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_afe14dd77dac0d9c78e1776ce852fae2.setIcon(icon_cc53a2e28d13c36ed68dbb3b8f80681a);
        
    
            marker_afe14dd77dac0d9c78e1776ce852fae2.bindTooltip(
                `<div>
                     STARTUPS: Skout
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_63062ada272750789111f0e7af540aa1 = L.marker(
                [37.737609, -122.435399],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_84858246fda9ad0bfac59c16e414ef4c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_63062ada272750789111f0e7af540aa1.setIcon(icon_84858246fda9ad0bfac59c16e414ef4c);
        
    
            marker_63062ada272750789111f0e7af540aa1.bindTooltip(
                `<div>
                     STARTUPS: CyberArts
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_109eea18c4996500238881fe26bee101 = L.marker(
                [37.787445, -122.397847],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7181ff1e78cf1d292a94f56133d3a979 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_109eea18c4996500238881fe26bee101.setIcon(icon_7181ff1e78cf1d292a94f56133d3a979);
        
    
            marker_109eea18c4996500238881fe26bee101.bindTooltip(
                `<div>
                     STARTUPS: Granicus
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_8dba5459c55a0d4968096383fcd9fac9 = L.marker(
                [37.7911371, -122.3973539],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_aa79f37735b9e6989d1a9d8a8f9ac650 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_8dba5459c55a0d4968096383fcd9fac9.setIcon(icon_aa79f37735b9e6989d1a9d8a8f9ac650);
        
    
            marker_8dba5459c55a0d4968096383fcd9fac9.bindTooltip(
                `<div>
                     STARTUPS: Glu Mobile
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_dde8b9231548bec436a95edfa6b05895 = L.marker(
                [37.785655, -122.398393],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b033a4ebd57149bc01545a3da565cd36 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_dde8b9231548bec436a95edfa6b05895.setIcon(icon_b033a4ebd57149bc01545a3da565cd36);
        
    
            marker_dde8b9231548bec436a95edfa6b05895.bindTooltip(
                `<div>
                     STARTUPS: Neocase Software
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_16b383ae33561ce8f8b83061aa6e9c26 = L.marker(
                [37.788796, -122.40971],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ba73d32f9e8b30e9463e7bd26b5dac27 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_16b383ae33561ce8f8b83061aa6e9c26.setIcon(icon_ba73d32f9e8b30e9463e7bd26b5dac27);
        
    
            marker_16b383ae33561ce8f8b83061aa6e9c26.bindTooltip(
                `<div>
                     STARTUPS: KODA
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_699d084c32d3259f542f75a27eabcc70 = L.marker(
                [37.795141, -122.401194],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ad4e9db23e08230a3d4bf30b5ea76fbb = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_699d084c32d3259f542f75a27eabcc70.setIcon(icon_ad4e9db23e08230a3d4bf30b5ea76fbb);
        
    
            marker_699d084c32d3259f542f75a27eabcc70.bindTooltip(
                `<div>
                     STARTUPS: Visage Mobile
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_1a1838254205ee36f1a403af366468bf = L.marker(
                [38.88521, -76.998641],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_3d0f8d4fa576a36b749aef4f81cb99dc = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_1a1838254205ee36f1a403af366468bf.setIcon(icon_3d0f8d4fa576a36b749aef4f81cb99dc);
        
    
            marker_1a1838254205ee36f1a403af366468bf.bindTooltip(
                `<div>
                     STARTUPS: Socialize
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e6f84a56f4739dea37973e8ff9ea6bae = L.marker(
                [59.42145, 24.8021233],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_895e4ef3c3419094da950248140f0578 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e6f84a56f4739dea37973e8ff9ea6bae.setIcon(icon_895e4ef3c3419094da950248140f0578);
        
    
            marker_e6f84a56f4739dea37973e8ff9ea6bae.bindTooltip(
                `<div>
                     STARTUPS: Fortumo
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_39cbb7c99d28d259099d47fa70ad3c69 = L.marker(
                [37.7870841, -122.4010675],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_80d85382b1e5f02e4ed8ea19fc1a5a63 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_39cbb7c99d28d259099d47fa70ad3c69.setIcon(icon_80d85382b1e5f02e4ed8ea19fc1a5a63);
        
    
            marker_39cbb7c99d28d259099d47fa70ad3c69.bindTooltip(
                `<div>
                     STARTUPS: Eightfold Logic
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_17ef7e0d4bc2fc20694a6a49df9bddfb = L.marker(
                [37.7905948, -122.406111],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a8a4e1a32a004daac9e745d6f8e5cf97 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_17ef7e0d4bc2fc20694a6a49df9bddfb.setIcon(icon_a8a4e1a32a004daac9e745d6f8e5cf97);
        
    
            marker_17ef7e0d4bc2fc20694a6a49df9bddfb.bindTooltip(
                `<div>
                     STARTUPS: PaperG
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_e952cfa127633cabd24155ada536781b = L.marker(
                [37.777995, -122.408914],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9adec4f3c86623046b517d23a773ba31 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_e952cfa127633cabd24155ada536781b.setIcon(icon_9adec4f3c86623046b517d23a773ba31);
        
    
            marker_e952cfa127633cabd24155ada536781b.bindTooltip(
                `<div>
                     STARTUPS: MyLikes
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_f5f06baf310e43e2ad717963fcbc8edd = L.marker(
                [55.6924605, 12.530107],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4106b68dc0e27c16a1ff4aa8d5149e50 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_f5f06baf310e43e2ad717963fcbc8edd.setIcon(icon_4106b68dc0e27c16a1ff4aa8d5149e50);
        
    
            marker_f5f06baf310e43e2ad717963fcbc8edd.bindTooltip(
                `<div>
                     STARTUPS: Unity Technologies
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5ff5919fdcfc037d2fe90db16e95eafc = L.marker(
                [37.7775896, -122.3956019],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ead99dce977b0d030c94c43f7bd0aeed = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5ff5919fdcfc037d2fe90db16e95eafc.setIcon(icon_ead99dce977b0d030c94c43f7bd0aeed);
        
    
            marker_5ff5919fdcfc037d2fe90db16e95eafc.bindTooltip(
                `<div>
                     STARTUPS: SuperSecret
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a6ab806a3e71bb092985012722f76f9e = L.marker(
                [37.7405936, -122.3764714],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e97339afd0847abc047a0276487a21f3 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a6ab806a3e71bb092985012722f76f9e.setIcon(icon_e97339afd0847abc047a0276487a21f3);
        
    
            marker_a6ab806a3e71bb092985012722f76f9e.bindTooltip(
                `<div>
                     STARTUPS: CoTweet
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_a291e447fb083b49af1a6e143e6a4566 = L.marker(
                [37.791128, -122.398126],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_11562614303c49146f963abc78c97818 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_a291e447fb083b49af1a6e143e6a4566.setIcon(icon_11562614303c49146f963abc78c97818);
        
    
            marker_a291e447fb083b49af1a6e143e6a4566.bindTooltip(
                `<div>
                     STARTUPS: Bomgar
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5aa388be5056f1994e1cad110e36383d = L.marker(
                [37.789471, -122.404113],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_f2c985d7214e3d8427fdf4aa8508fad2 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5aa388be5056f1994e1cad110e36383d.setIcon(icon_f2c985d7214e3d8427fdf4aa8508fad2);
        
    
            marker_5aa388be5056f1994e1cad110e36383d.bindTooltip(
                `<div>
                     STARTUPS: Birst
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_3757798785f6fc7d886cf40c087f5a1a = L.marker(
                [37.7550372, -122.4264417],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_b6a6081101b7a524b0f211350e0cd921 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_3757798785f6fc7d886cf40c087f5a1a.setIcon(icon_b6a6081101b7a524b0f211350e0cd921);
        
    
            marker_3757798785f6fc7d886cf40c087f5a1a.bindTooltip(
                `<div>
                     STARTUPS: Netbiscuits
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_87e661f685c36baadfd16c9b13c6b7ab = L.marker(
                [37.7795014, -122.3948712],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_4ae6d3562c442cf8947aefd100e314dd = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_87e661f685c36baadfd16c9b13c6b7ab.setIcon(icon_4ae6d3562c442cf8947aefd100e314dd);
        
    
            marker_87e661f685c36baadfd16c9b13c6b7ab.bindTooltip(
                `<div>
                     STARTUPS: Fotomoto
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_baf8df1a40c4cac4dcf625b8e087aa28 = L.marker(
                [37.791388, -122.392148],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2365cb860fa36447d327fd04ddc98001 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_baf8df1a40c4cac4dcf625b8e087aa28.setIcon(icon_2365cb860fa36447d327fd04ddc98001);
        
    
            marker_baf8df1a40c4cac4dcf625b8e087aa28.bindTooltip(
                `<div>
                     STARTUPS: Canopy Financial
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9ff0123a5d23a0812a20894f455c2dd6 = L.marker(
                [37.788864, -122.4008487],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e6e78101f2834e1c1ec4e864de74ef83 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9ff0123a5d23a0812a20894f455c2dd6.setIcon(icon_e6e78101f2834e1c1ec4e864de74ef83);
        
    
            marker_9ff0123a5d23a0812a20894f455c2dd6.bindTooltip(
                `<div>
                     STARTUPS: Marketcetera
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cfafa0832a71b9c6f50883a03d3f5498 = L.marker(
                [51.865488, -2.235187],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9371a7149b9cdd6cc38a0cd3e3c11661 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cfafa0832a71b9c6f50883a03d3f5498.setIcon(icon_9371a7149b9cdd6cc38a0cd3e3c11661);
        
    
            marker_cfafa0832a71b9c6f50883a03d3f5498.bindTooltip(
                `<div>
                     STARTUPS: RainStor
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_c4aa5c920003624c47806c645e831a07 = L.marker(
                [37.7870283, -122.4000192],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a1ed2a3acd0e8037f3664fa1e865a6da = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_c4aa5c920003624c47806c645e831a07.setIcon(icon_a1ed2a3acd0e8037f3664fa1e865a6da);
        
    
            marker_c4aa5c920003624c47806c645e831a07.bindTooltip(
                `<div>
                     STARTUPS: Playboox
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_fd6fb698cbdf6a6b2223911781e3ca16 = L.marker(
                [37.7697623, -122.4183929],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_693c69174d1c03ade54b733d42d41b9d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_fd6fb698cbdf6a6b2223911781e3ca16.setIcon(icon_693c69174d1c03ade54b733d42d41b9d);
        
    
            marker_fd6fb698cbdf6a6b2223911781e3ca16.bindTooltip(
                `<div>
                     STARTUPS: PlantSense
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_786373a74955cc97a63b7a0eda9b4149 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_e1e235ff3b7f2859cfa7171dd5f8d781 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_786373a74955cc97a63b7a0eda9b4149.setIcon(icon_e1e235ff3b7f2859cfa7171dd5f8d781);
        
    
            marker_786373a74955cc97a63b7a0eda9b4149.bindTooltip(
                `<div>
                     STARTUPS: Topsy Labs
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_7e5e537391ddb0499294d1e54e8e6f14 = L.marker(
                [37.7676285, -122.393799],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_45153ecbcda8ea89f35f0a972957bfb0 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_7e5e537391ddb0499294d1e54e8e6f14.setIcon(icon_45153ecbcda8ea89f35f0a972957bfb0);
        
    
            marker_7e5e537391ddb0499294d1e54e8e6f14.bindTooltip(
                `<div>
                     STARTUPS: Five Prime Therapeutics
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_818d00b6d37e41e28d8bf8602a4eb6d2 = L.marker(
                [37.775206, -122.419209],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_1e81e6997f17b940045edd8b9e6bbf2c = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_818d00b6d37e41e28d8bf8602a4eb6d2.setIcon(icon_1e81e6997f17b940045edd8b9e6bbf2c);
        
    
            marker_818d00b6d37e41e28d8bf8602a4eb6d2.bindTooltip(
                `<div>
                     STARTUPS: Fliptop
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2aeab4004338f3bd22a493fc276d45ce = L.marker(
                [37.7871306, -122.4041075],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_2942d8255832d915205a8fa4bbd6193a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2aeab4004338f3bd22a493fc276d45ce.setIcon(icon_2942d8255832d915205a8fa4bbd6193a);
        
    
            marker_2aeab4004338f3bd22a493fc276d45ce.bindTooltip(
                `<div>
                     STARTUPS: Sunrun
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_16ec06c641b2e3464a49d4d8f9e1bf3e = L.marker(
                [37.7871306, -122.4041075],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_be5a7f39b96c54dd52166dcd9c9ac75d = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_16ec06c641b2e3464a49d4d8f9e1bf3e.setIcon(icon_be5a7f39b96c54dd52166dcd9c9ac75d);
        
    
            marker_16ec06c641b2e3464a49d4d8f9e1bf3e.bindTooltip(
                `<div>
                     STARTUPS: ACT Biotech
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_5c3ccd1b2e5f7abada30999ed5590ddc = L.marker(
                [37.805289, -122.404448],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_924d2af6606587a2a3c5bb29b3ce1a84 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_5c3ccd1b2e5f7abada30999ed5590ddc.setIcon(icon_924d2af6606587a2a3c5bb29b3ce1a84);
        
    
            marker_5c3ccd1b2e5f7abada30999ed5590ddc.bindTooltip(
                `<div>
                     STARTUPS: Recurrent Energy
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2657bbcb3b73b72c9ccc5883ab8abf46 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_a4dc28d88f88373c535fc9d5471b863b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2657bbcb3b73b72c9ccc5883ab8abf46.setIcon(icon_a4dc28d88f88373c535fc9d5471b863b);
        
    
            marker_2657bbcb3b73b72c9ccc5883ab8abf46.bindTooltip(
                `<div>
                     STARTUPS: Autonomic Technologies
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_47ad63a3eb6a77ced4aba3a2e2375153 = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_77506fe38c86a4ed5a9fc32fd1071134 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_47ad63a3eb6a77ced4aba3a2e2375153.setIcon(icon_77506fe38c86a4ed5a9fc32fd1071134);
        
    
            marker_47ad63a3eb6a77ced4aba3a2e2375153.bindTooltip(
                `<div>
                     STARTUPS: Osprey Pharmaceuticals USA
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_be3cbf7e290e1db73de4d6fe69476a4a = L.marker(
                [37.767575, -122.411157],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7dae49177149e7d3f040e0a9d2ecf78a = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_be3cbf7e290e1db73de4d6fe69476a4a.setIcon(icon_7dae49177149e7d3f040e0a9d2ecf78a);
        
    
            marker_be3cbf7e290e1db73de4d6fe69476a4a.bindTooltip(
                `<div>
                     STARTUPS: UTOPY
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_4dd9f08b4a2db25fb64b623cdc7ac4f6 = L.marker(
                [37.4484914, -122.1802812],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_fa083a9b5b7ce0c6e2cb29dcf3ac9be0 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_4dd9f08b4a2db25fb64b623cdc7ac4f6.setIcon(icon_fa083a9b5b7ce0c6e2cb29dcf3ac9be0);
        
    
            marker_4dd9f08b4a2db25fb64b623cdc7ac4f6.bindTooltip(
                `<div>
                     STARTUPS: Siluria Technologies
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2c3d7a8cfc3d2664eeab16e098d56db8 = L.marker(
                [37.781689, -122.391061],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7e356a2d5bfd43f9a44cca4178c3e56f = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2c3d7a8cfc3d2664eeab16e098d56db8.setIcon(icon_7e356a2d5bfd43f9a44cca4178c3e56f);
        
    
            marker_2c3d7a8cfc3d2664eeab16e098d56db8.bindTooltip(
                `<div>
                     STARTUPS: ZoomSystems
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_9627ca9c8a905e671127977ccd39de91 = L.marker(
                [37.787646, -122.402759],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_14625e131def41b638d163412857aade = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_9627ca9c8a905e671127977ccd39de91.setIcon(icon_14625e131def41b638d163412857aade);
        
    
            marker_9627ca9c8a905e671127977ccd39de91.bindTooltip(
                `<div>
                     STARTUPS: Exent
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_2665f69a24f47f5c4ec6e2a73e0b153d = L.marker(
                [37.775196, -122.419204],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_ade44694a3843de8a0af71340a7e328b = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_2665f69a24f47f5c4ec6e2a73e0b153d.setIcon(icon_ade44694a3843de8a0af71340a7e328b);
        
    
            marker_2665f69a24f47f5c4ec6e2a73e0b153d.bindTooltip(
                `<div>
                     STARTUPS: Scout Labs
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_cfe25879798b64c612efbf55f06587b9 = L.marker(
                [37.7869047, -122.4043924],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7661342e15b1c7a6820f2318ec9736ed = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_cfe25879798b64c612efbf55f06587b9.setIcon(icon_7661342e15b1c7a6820f2318ec9736ed);
        
    
            marker_cfe25879798b64c612efbf55f06587b9.bindTooltip(
                `<div>
                     STARTUPS: TeachScape
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_99aef61e6899e9ceb40296c70f18d52c = L.marker(
                [37.782475, -122.407764],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_7baefe31d651db2923f7759f8d5bb5e5 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "usd", "iconColor": "white", "markerColor": "green", "prefix": "fa"}
            );
            marker_99aef61e6899e9ceb40296c70f18d52c.setIcon(icon_7baefe31d651db2923f7759f8d5bb5e5);
        
    
            marker_99aef61e6899e9ceb40296c70f18d52c.bindTooltip(
                `<div>
                     STARTUPS: Roc2Loc
                 </div>`,
                {"sticky": true}
            );
        
    
            var marker_b1d758d60ed2847527acb13e637c3715 = L.marker(
                [37.7955307, -122.4005983],
                {}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
            var icon_9267ae1a1d9907120ea875ceca9b30e6 = L.AwesomeMarkers.icon(
                {"extraClasses": "fa-rotate-0", "icon": "tint", "iconColor": "white", "markerColor": "beige", "prefix": "fa"}
            );
            marker_b1d758d60ed2847527acb13e637c3715.setIcon(icon_9267ae1a1d9907120ea875ceca9b30e6);
        
    
            marker_b1d758d60ed2847527acb13e637c3715.bindTooltip(
                `<div>
                     DESIGN COMPANY
                 </div>`,
                {"sticky": true}
            );
        
    
            var circle_6b571bfff5a08aa683128ae19daf0c8c = L.circle(
                [37.7804301, -122.4103305],
                {"bubblingMouseEvents": true, "color": "green", "dashArray": null, "dashOffset": null, "fill": true, "fillColor": "green", "fillOpacity": 0.2, "fillRule": "evenodd", "lineCap": "round", "lineJoin": "round", "opacity": 1.0, "radius": 3500, "stroke": true, "weight": 2}
            ).addTo(map_22f027bd92e556e9840de6a9a2c8de6d);
        
    
        var popup_2e33914dfe8289e58e2dcace48e53ece = L.popup({"maxWidth": "100%"});

        
            
                var html_213c05d5b6a4521ff911767a93821d2c = $(`<div id="html_213c05d5b6a4521ff911767a93821d2c" style="width: 100.0%; height: 100.0%;">Desired Area</div>`)[0];
                popup_2e33914dfe8289e58e2dcace48e53ece.setContent(html_213c05d5b6a4521ff911767a93821d2c);
            
        

        circle_6b571bfff5a08aa683128ae19daf0c8c.bindPopup(popup_2e33914dfe8289e58e2dcace48e53ece)
        ;

        
    
</script>
</html>
```

![Separate Heatmaps](image/heatmap_separate_venues.html)

The heatmap shows that the area right bellow the location of the Design Company concentrates the most services that the employees deem important, narrowing the selection of possible offices to:
 - Exente: 685, Market Street
 - hi5: 55 Second Street
 - Twilio: 645, Harrison
 - eBuddy: 55, Post Street

### Choosing a location :pushpin:

When pinpointing all the venues to cover the staff requirements, it becomes evident that the EXENT office is the one that has the most services available in the shortest distances and is closest to second best rated vegan restaurant in the city. The location also is 2 blocks away from a Park, so staff could enjoy meals outdoors and relax. Acess to the San Francisco International Airport is facilitated by the train station. There is also a Ferry Station nearby if necessary.

To make the maintenace guy happy, a Basketball Stadium is within 2,5km as shown in the map.

![](image/final_plot.jpg)

![Final Decision Map](image/narrowing_venues.html)

## Conclusion

:white_check_mark: There must be some nearby companies that also do design.

:x: 30% of the company staff have at least 1 child. - The nearest school is several blocks away :pleading_face:

:white_check_mark: It should be near successful tech startups that have raised at least US$1M.

:white_check_mark: Executives like Starbucks A LOT.

:white_check_mark: Account managers need to travel a lot.

:white_check_mark: Everyone in the company is between 25 and 40, give them some place to go party.

:white_check_mark: The CEO is vegan.

:white_check_mark: If you want to make the maintenance guy happy, a basketball stadium must be around 10 Km.

:white_check_mark: The office dog—"Dobby" needs a hairdresser every month. Ensure there's one not too far away.

![](https://i.pinimg.com/originals/1c/b0/5e/1cb05ecc2f4f17013e6d574834044585.jpg)


### Coordinates
Exent -- 
latitude: 37.787646
longitude: -122.402759

## Technologies
For this project eight different libraries were used:
- Pymongo
- Pandas
- Getpass
- Dotenv
- OS module
- Requests
- Json
- Folium

