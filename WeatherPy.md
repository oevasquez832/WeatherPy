
# WeatherPy
## Analysis
#### Temperature reaches a maximum as it approaches the equator, and so there is a clear relationship between increasing temperature and proximity to the equator
#### Distribution of humidity around the equator is similar to other latitudes ranging from 20-100
#### Cloudiness, like humidity is spread out among the latitudes and does not display a clear relationship
#### The highest concentration of hotter temperatures appear along the equator


```python
#Import Dependencies
import config
from citipy import citipy

import random
import requests

import pandas as pd
import numpy as np

import matplotlib.pyplot as plt

import seaborn as sns
```

# Get all the samples!


```python
# Collecting a sample of world cities.

# Set Lat & Lon Zones to randomly pick coordinates
lat_zone = np.arange(-90,90,15)
lon_zone = np.arange(-200,200,15)


# Create a new data frame to hold picked coordinates and city info
cities_df = pd.DataFrame()

cities_df["Latitude"] = ""
cities_df["Longitude"] = ""

# From each zone, randomly pick 50 unique coordinates and add to dataframe.
for x in lat_zone:
    for y in lon_zone:
        x_values = list(np.arange(x,x+15,0.01))
        y_values = list(np.arange(y,y+15,0.01))
        lats = random.sample(x_values,50)
        lons = random.sample(y_values,50)
        lat_samples = [(x+dec_lat) for dec_lat in lats]
        lon_samples = [y+dec_lon for dec_lon in lons]
        cities_df = cities_df.append(pd.DataFrame.from_dict({"Latitude":lat_samples,
                                       "Longitude":lon_samples}))
cities_df = cities_df.reset_index(drop=True)
cities_df.shape
```




    (16200, 2)




```python
# For the selected coordinates, use citipy to associate them with nearest city.
cities_df["Closest City name"] = ""
cities_df["Closest Country code"] = ""
for index,row in cities_df.iterrows():
    city = citipy.nearest_city(row["Latitude"],row["Longitude"])
    cities_df.set_value(index,"Closest City name",city.city_name)
    cities_df.set_value(index,"Closest Country code",city.country_code)

# Drop random lat and lon samples from dataframe that don't match coordinates
clean_cities_df = cities_df.drop(['Latitude', 'Longitude'],axis=1)
clean_cities_df

# Drop duplicate cities 
clean_cities_df = clean_cities_df.drop_duplicates()

# Pick a sample of 500 cities for analysis
select_cities = clean_cities_df.sample(500)
select_cities = select_cities.reset_index(drop=True)
```

# Hello? OpenWeatherMap? You there?


```python
# Set up values for OpenWeatherMap API calls
base_url = "http://api.openweathermap.org/data/2.5/weather"
params = { "appid" :app_id,"units":"metric" }

```


```python
def encrypt_key(base_url):
    return base_url[0:53]+config.api_key+base_url[85:]

#GET information from API request and output log
for index,row in selected_cities.iterrows():
    params["q"] =f'{row["Closest City name"]},{row["Closest Country code"]}'
    print(f"Retrieving weather information for {params['q']}")
    city_weather_resp = requests.get(base_url,params)
    print(encrypt_key(city_weather_resp.url))
    city_weather_resp  = city_weather_resp.json()
    select_cities.set_value(index,"Latitude",city_weather_resp.get("coord",{}).get("lat"))
    select_cities.set_value(index,"Longitude",city_weather_resp.get("coord",{}).get("lon"))
    select_cities.set_value(index,"Temperature",city_weather_resp.get("main",{}).get("temp_max"))
    select_cities.set_value(index,"Wind speed",city_weather_resp.get("wind",{}).get("speed"))
    select_cities.set_value(index,"Humidity",city_weather_resp.get("main",{}).get("humidity"))
    select_cities.set_value(index,"Cloudiness",city_weather_resp.get("clouds",{}).get("all"))

```

    Retrieving weather information for homer,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=homer%2Cus
    Retrieving weather information for oriximina,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=oriximina%2Cbr
    Retrieving weather information for kamarion,gr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kamarion%2Cgr
    Retrieving weather information for geraldton,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=geraldton%2Cau
    Retrieving weather information for clyde river,ca
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=clyde+river%2Cca
    Retrieving weather information for ceuta,es
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ceuta%2Ces
    Retrieving weather information for port hawkesbury,ca
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=port+hawkesbury%2Cca
    Retrieving weather information for kumi,ug
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kumi%2Cug
    Retrieving weather information for lakselv,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=lakselv%2Cno
    Retrieving weather information for zelenoborsk,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=zelenoborsk%2Cru
    Retrieving weather information for nizhniy kuranakh,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=nizhniy+kuranakh%2Cru
    Retrieving weather information for figeac,fr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=figeac%2Cfr
    Retrieving weather information for mut,tr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mut%2Ctr
    Retrieving weather information for grand gaube,mu
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=grand+gaube%2Cmu
    Retrieving weather information for bowen,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bowen%2Cau
    Retrieving weather information for hambantota,lk
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hambantota%2Clk
    Retrieving weather information for xai-xai,mz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=xai-xai%2Cmz
    Retrieving weather information for emmett,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=emmett%2Cus
    Retrieving weather information for chengde,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=chengde%2Ccn
    Retrieving weather information for la paz,bo
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=la+paz%2Cbo
    Retrieving weather information for kayerkan,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kayerkan%2Cru
    Retrieving weather information for nicoya,cr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=nicoya%2Ccr
    Retrieving weather information for korla,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=korla%2Ccn
    Retrieving weather information for nikolskoye,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=nikolskoye%2Cru
    Retrieving weather information for changji,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=changji%2Ccn
    Retrieving weather information for kintampo,gh
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kintampo%2Cgh
    Retrieving weather information for la asuncion,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=la+asuncion%2Cve
    Retrieving weather information for razole,in
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=razole%2Cin
    Retrieving weather information for romitan,uz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=romitan%2Cuz
    Retrieving weather information for zabol,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=zabol%2Cir
    Retrieving weather information for hilo,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hilo%2Cus
    Retrieving weather information for liverpool,ca
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=liverpool%2Cca
    Retrieving weather information for sovetskiy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sovetskiy%2Cru
    Retrieving weather information for atascadero,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=atascadero%2Cus
    Retrieving weather information for jining,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=jining%2Ccn
    Retrieving weather information for balabac,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=balabac%2Cph
    Retrieving weather information for san lorenzo,bo
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=san+lorenzo%2Cbo
    Retrieving weather information for half moon bay,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=half+moon+bay%2Cus
    Retrieving weather information for salalah,om
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=salalah%2Com
    Retrieving weather information for puerto ayacucho,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=puerto+ayacucho%2Cve
    Retrieving weather information for troitsko-pechorsk,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=troitsko-pechorsk%2Cru
    Retrieving weather information for spornoye,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=spornoye%2Cru
    Retrieving weather information for sabzevar,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sabzevar%2Cir
    Retrieving weather information for qesarya,il
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=qesarya%2Cil
    Retrieving weather information for saint-joseph,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saint-joseph%2Cre
    Retrieving weather information for zhuanghe,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=zhuanghe%2Ccn
    Retrieving weather information for leningradskiy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=leningradskiy%2Cru
    Retrieving weather information for vila franca do campo,pt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vila+franca+do+campo%2Cpt
    Retrieving weather information for borovoy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=borovoy%2Cru
    Retrieving weather information for tawau,my
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tawau%2Cmy
    Retrieving weather information for karaj,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=karaj%2Cir
    Retrieving weather information for broome,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=broome%2Cau
    Retrieving weather information for poros,gr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=poros%2Cgr
    Retrieving weather information for jiehu,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=jiehu%2Ccn
    Retrieving weather information for kemi,fi
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kemi%2Cfi
    Retrieving weather information for phuthaditjhaba,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=phuthaditjhaba%2Cza
    Retrieving weather information for saint george,bm
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saint+george%2Cbm
    Retrieving weather information for boa vista,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=boa+vista%2Cbr
    Retrieving weather information for cape town,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=cape+town%2Cza
    Retrieving weather information for elko,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=elko%2Cus
    Retrieving weather information for dukat,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=dukat%2Cru
    Retrieving weather information for gladstone,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=gladstone%2Cau
    Retrieving weather information for tateyama,jp
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tateyama%2Cjp
    Retrieving weather information for ligayan,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ligayan%2Cph
    Retrieving weather information for severo-kurilsk,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=severo-kurilsk%2Cru
    Retrieving weather information for lombog,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=lombog%2Cph
    Retrieving weather information for kaeo,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kaeo%2Cnz
    Retrieving weather information for harmanli,bg
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=harmanli%2Cbg
    Retrieving weather information for mmabatho,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mmabatho%2Cza
    Retrieving weather information for barstow,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=barstow%2Cus
    Retrieving weather information for primorsk,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=primorsk%2Cru
    Retrieving weather information for pochutla,mx
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pochutla%2Cmx
    Retrieving weather information for qaanaaq,gl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=qaanaaq%2Cgl
    Retrieving weather information for avarua,ck
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=avarua%2Cck
    Retrieving weather information for piacabucu,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=piacabucu%2Cbr
    Retrieving weather information for east london,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=east+london%2Cza
    Retrieving weather information for sterling,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sterling%2Cus
    Retrieving weather information for zapolyarnyy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=zapolyarnyy%2Cru
    Retrieving weather information for rawson,ar
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=rawson%2Car
    Retrieving weather information for saint-pierre,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saint-pierre%2Cre
    Retrieving weather information for farah,af
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=farah%2Caf
    Retrieving weather information for kavaratti,in
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kavaratti%2Cin
    Retrieving weather information for mabaruma,gy
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mabaruma%2Cgy
    Retrieving weather information for port blair,in
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=port+blair%2Cin
    Retrieving weather information for katima mulilo,na
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=katima+mulilo%2Cna
    Retrieving weather information for tupelo,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tupelo%2Cus
    Retrieving weather information for lookan,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=lookan%2Cph
    Retrieving weather information for busselton,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=busselton%2Cau
    Retrieving weather information for kaka,tm
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kaka%2Ctm
    Retrieving weather information for hohhot,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hohhot%2Ccn
    Retrieving weather information for hofn,is
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hofn%2Cis
    Retrieving weather information for innisfail,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=innisfail%2Cau
    Retrieving weather information for san-pedro,ci
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=san-pedro%2Cci
    Retrieving weather information for puerto madero,mx
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=puerto+madero%2Cmx
    Retrieving weather information for pravia,es
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pravia%2Ces
    Retrieving weather information for seymchan,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=seymchan%2Cru
    Retrieving weather information for arcata,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=arcata%2Cus
    Retrieving weather information for meulaboh,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=meulaboh%2Cid
    Retrieving weather information for serta,pt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=serta%2Cpt
    Retrieving weather information for goshogawara,jp
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=goshogawara%2Cjp
    Retrieving weather information for totness,sr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=totness%2Csr
    Retrieving weather information for nigde,tr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=nigde%2Ctr
    Retrieving weather information for pleasant grove,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pleasant+grove%2Cus
    Retrieving weather information for berlevag,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=berlevag%2Cno
    Retrieving weather information for mmathubudukwane,bw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mmathubudukwane%2Cbw
    Retrieving weather information for silifke,tr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=silifke%2Ctr
    Retrieving weather information for talnakh,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=talnakh%2Cru
    Retrieving weather information for chokurdakh,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=chokurdakh%2Cru
    Retrieving weather information for palu,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=palu%2Cid
    Retrieving weather information for miri,my
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=miri%2Cmy
    Retrieving weather information for kudahuvadhoo,mv
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kudahuvadhoo%2Cmv
    Retrieving weather information for sabang,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sabang%2Cid
    Retrieving weather information for beitbridge,zw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=beitbridge%2Czw
    Retrieving weather information for shudayag,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=shudayag%2Cru
    Retrieving weather information for vila velha,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vila+velha%2Cbr
    Retrieving weather information for siguiri,gn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=siguiri%2Cgn
    Retrieving weather information for linhares,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=linhares%2Cbr
    Retrieving weather information for mayor pablo lagerenza,py
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mayor+pablo+lagerenza%2Cpy
    Retrieving weather information for padilla,bo
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=padilla%2Cbo
    Retrieving weather information for bondo,cd
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bondo%2Ccd
    Retrieving weather information for belmonte,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=belmonte%2Cbr
    Retrieving weather information for adre,td
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=adre%2Ctd
    Retrieving weather information for posadas,ar
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=posadas%2Car
    Retrieving weather information for gondanglegi,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=gondanglegi%2Cid
    Retrieving weather information for bintulu,my
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bintulu%2Cmy
    Retrieving weather information for rodez,fr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=rodez%2Cfr
    Retrieving weather information for aketi,cd
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=aketi%2Ccd
    Retrieving weather information for pahrump,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pahrump%2Cus
    Retrieving weather information for kortkeros,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kortkeros%2Cru
    Retrieving weather information for pacifica,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pacifica%2Cus
    Retrieving weather information for sao filipe,cv
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sao+filipe%2Ccv
    Retrieving weather information for beysehir,tr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=beysehir%2Ctr
    Retrieving weather information for bucu,ro
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bucu%2Cro
    Retrieving weather information for santiago del estero,ar
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=santiago+del+estero%2Car
    Retrieving weather information for rocha,uy
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=rocha%2Cuy
    Retrieving weather information for calabozo,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=calabozo%2Cve
    Retrieving weather information for malamig,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=malamig%2Cph
    Retrieving weather information for dingzhou,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=dingzhou%2Ccn
    Retrieving weather information for bud,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bud%2Cno
    Retrieving weather information for itacurubi del rosario,py
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=itacurubi+del+rosario%2Cpy
    Retrieving weather information for tevaitoa,pf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tevaitoa%2Cpf
    Retrieving weather information for sisimiut,gl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sisimiut%2Cgl
    Retrieving weather information for la ciotat,fr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=la+ciotat%2Cfr
    Retrieving weather information for saldanha,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saldanha%2Cza
    Retrieving weather information for vao,nc
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vao%2Cnc
    Retrieving weather information for atherton,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=atherton%2Cau
    Retrieving weather information for mirnyy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mirnyy%2Cru
    Retrieving weather information for waitati,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=waitati%2Cnz
    Retrieving weather information for union,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=union%2Cph
    Retrieving weather information for truckee,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=truckee%2Cus
    Retrieving weather information for saint-philippe,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saint-philippe%2Cre
    Retrieving weather information for kita,ml
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kita%2Cml
    Retrieving weather information for victoria,sc
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=victoria%2Csc
    Retrieving weather information for garmsar,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=garmsar%2Cir
    Retrieving weather information for teguldet,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=teguldet%2Cru
    Retrieving weather information for palamos,es
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=palamos%2Ces
    Retrieving weather information for sinazongwe,zm
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sinazongwe%2Czm
    Retrieving weather information for bousse,bf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bousse%2Cbf
    Retrieving weather information for lebanon,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=lebanon%2Cus
    Retrieving weather information for kuryk,kz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kuryk%2Ckz
    Retrieving weather information for saint-louis,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saint-louis%2Cre
    Retrieving weather information for coos bay,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=coos+bay%2Cus
    Retrieving weather information for castro,cl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=castro%2Ccl
    Retrieving weather information for dikson,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=dikson%2Cru
    Retrieving weather information for taywarah,af
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=taywarah%2Caf
    Retrieving weather information for luohe,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=luohe%2Ccn
    Retrieving weather information for huarmey,pe
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=huarmey%2Cpe
    Retrieving weather information for manado,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=manado%2Cid
    Retrieving weather information for ierapetra,gr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ierapetra%2Cgr
    Retrieving weather information for cidreira,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=cidreira%2Cbr
    Retrieving weather information for champerico,gt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=champerico%2Cgt
    Retrieving weather information for lompoc,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=lompoc%2Cus
    Retrieving weather information for viedma,ar
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=viedma%2Car
    Retrieving weather information for kushiro,jp
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kushiro%2Cjp
    Retrieving weather information for ghanzi,bw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ghanzi%2Cbw
    Retrieving weather information for lebu,cl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=lebu%2Ccl
    Retrieving weather information for upata,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=upata%2Cve
    Retrieving weather information for maniitsoq,gl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=maniitsoq%2Cgl
    Retrieving weather information for huntsville,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=huntsville%2Cus
    Retrieving weather information for ilulissat,gl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ilulissat%2Cgl
    Retrieving weather information for koutiala,ml
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=koutiala%2Cml
    Retrieving weather information for christchurch,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=christchurch%2Cnz
    Retrieving weather information for kalmunai,lk
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kalmunai%2Clk
    Retrieving weather information for ngunguru,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ngunguru%2Cnz
    Retrieving weather information for ahtopol,bg
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ahtopol%2Cbg
    Retrieving weather information for anadyr,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=anadyr%2Cru
    Retrieving weather information for teahupoo,pf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=teahupoo%2Cpf
    Retrieving weather information for luderitz,na
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=luderitz%2Cna
    Retrieving weather information for monte gordo,pt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=monte+gordo%2Cpt
    Retrieving weather information for birao,cf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=birao%2Ccf
    Retrieving weather information for kozhva,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kozhva%2Cru
    Retrieving weather information for tuktoyaktuk,ca
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tuktoyaktuk%2Cca
    Retrieving weather information for sainte-rose,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sainte-rose%2Cre
    Retrieving weather information for aykhal,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=aykhal%2Cru
    Retrieving weather information for san jose,cr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=san+jose%2Ccr
    Retrieving weather information for abeche,td
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=abeche%2Ctd
    Retrieving weather information for taxiarkhis,gr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=taxiarkhis%2Cgr
    Retrieving weather information for muskogee,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=muskogee%2Cus
    Retrieving weather information for mahibadhoo,mv
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mahibadhoo%2Cmv
    Retrieving weather information for la rioja,ar
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=la+rioja%2Car
    Retrieving weather information for djibo,bf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=djibo%2Cbf
    Retrieving weather information for changqing,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=changqing%2Ccn
    Retrieving weather information for ciudad guayana,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ciudad+guayana%2Cve
    Retrieving weather information for chikwawa,mw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=chikwawa%2Cmw
    Retrieving weather information for bandarbeyla,so
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bandarbeyla%2Cso
    Retrieving weather information for havoysund,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=havoysund%2Cno
    Retrieving weather information for fort madison,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=fort+madison%2Cus
    Retrieving weather information for denison,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=denison%2Cus
    Retrieving weather information for luangwa,zm
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=luangwa%2Czm
    Retrieving weather information for floro,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=floro%2Cno
    Retrieving weather information for fallon,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=fallon%2Cus
    Retrieving weather information for prattville,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=prattville%2Cus
    Retrieving weather information for yenagoa,ng
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=yenagoa%2Cng
    Retrieving weather information for pacific grove,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pacific+grove%2Cus
    Retrieving weather information for tamale,gh
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tamale%2Cgh
    Retrieving weather information for vangaindrano,mg
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vangaindrano%2Cmg
    Retrieving weather information for chalus,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=chalus%2Cir
    Retrieving weather information for kodiak,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kodiak%2Cus
    Retrieving weather information for new norfolk,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=new+norfolk%2Cau
    Retrieving weather information for igarka,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=igarka%2Cru
    Retrieving weather information for ribeira grande,pt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ribeira+grande%2Cpt
    Retrieving weather information for gazli,uz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=gazli%2Cuz
    Retrieving weather information for bulembu,sz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bulembu%2Csz
    Retrieving weather information for delmas,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=delmas%2Cza
    Retrieving weather information for mehamn,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mehamn%2Cno
    Retrieving weather information for groningen,sr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=groningen%2Csr
    Retrieving weather information for alakurtti,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=alakurtti%2Cru
    Retrieving weather information for roma,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=roma%2Cau
    Retrieving weather information for honningsvag,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=honningsvag%2Cno
    Retrieving weather information for jumla,np
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=jumla%2Cnp
    Retrieving weather information for ilave,pe
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ilave%2Cpe
    Retrieving weather information for sturgeon bay,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sturgeon+bay%2Cus
    Retrieving weather information for dudinka,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=dudinka%2Cru
    Retrieving weather information for acajutla,sv
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=acajutla%2Csv
    Retrieving weather information for constitucion,mx
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=constitucion%2Cmx
    Retrieving weather information for kudat,my
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kudat%2Cmy
    Retrieving weather information for koygorodok,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=koygorodok%2Cru
    Retrieving weather information for filadelfia,py
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=filadelfia%2Cpy
    Retrieving weather information for kulhudhuffushi,mv
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kulhudhuffushi%2Cmv
    Retrieving weather information for patacamaya,bo
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=patacamaya%2Cbo
    Retrieving weather information for gisborne,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=gisborne%2Cnz
    Retrieving weather information for saint-leu,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saint-leu%2Cre
    Retrieving weather information for egvekinot,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=egvekinot%2Cru
    Retrieving weather information for baruun-urt,mn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=baruun-urt%2Cmn
    Retrieving weather information for mhlume,sz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mhlume%2Csz
    Retrieving weather information for malvern,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=malvern%2Cus
    Retrieving weather information for manjacaze,mz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=manjacaze%2Cmz
    Retrieving weather information for koutsouras,gr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=koutsouras%2Cgr
    Retrieving weather information for akdepe,tm
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=akdepe%2Ctm
    Retrieving weather information for reinosa,es
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=reinosa%2Ces
    Retrieving weather information for buchanan,lr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=buchanan%2Clr
    Retrieving weather information for yamada,jp
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=yamada%2Cjp
    Retrieving weather information for agirish,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=agirish%2Cru
    Retrieving weather information for bethel,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bethel%2Cus
    Retrieving weather information for karpathos,gr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=karpathos%2Cgr
    Retrieving weather information for trincomalee,lk
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=trincomalee%2Clk
    Retrieving weather information for puerto quijarro,bo
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=puerto+quijarro%2Cbo
    Retrieving weather information for obo,cf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=obo%2Ccf
    Retrieving weather information for uhlove,ua
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=uhlove%2Cua
    Retrieving weather information for polyarnyy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=polyarnyy%2Cru
    Retrieving weather information for starkville,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=starkville%2Cus
    Retrieving weather information for fortuna,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=fortuna%2Cus
    Retrieving weather information for erenhot,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=erenhot%2Ccn
    Retrieving weather information for snezhnogorsk,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=snezhnogorsk%2Cru
    Retrieving weather information for caravelas,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=caravelas%2Cbr
    Retrieving weather information for weinan,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=weinan%2Ccn
    Retrieving weather information for bluff,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bluff%2Cnz
    Retrieving weather information for chimore,bo
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=chimore%2Cbo
    Retrieving weather information for birnin kebbi,ng
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=birnin+kebbi%2Cng
    Retrieving weather information for labuhan,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=labuhan%2Cid
    Retrieving weather information for chipinge,zw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=chipinge%2Czw
    Retrieving weather information for canton,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=canton%2Cus
    Retrieving weather information for talaya,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=talaya%2Cru
    Retrieving weather information for kokkola,fi
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kokkola%2Cfi
    Retrieving weather information for mahebourg,mu
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mahebourg%2Cmu
    Retrieving weather information for prieska,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=prieska%2Cza
    Retrieving weather information for touros,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=touros%2Cbr
    Retrieving weather information for urubicha,bo
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=urubicha%2Cbo
    Retrieving weather information for tinaquillo,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tinaquillo%2Cve
    Retrieving weather information for pozo colorado,py
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pozo+colorado%2Cpy
    Retrieving weather information for vilyuysk,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vilyuysk%2Cru
    Retrieving weather information for joshimath,in
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=joshimath%2Cin
    Retrieving weather information for onalaska,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=onalaska%2Cus
    Retrieving weather information for alyangula,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=alyangula%2Cau
    Retrieving weather information for iquique,cl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=iquique%2Ccl
    Retrieving weather information for togur,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=togur%2Cru
    Retrieving weather information for kavieng,pg
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kavieng%2Cpg
    Retrieving weather information for save,bj
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=save%2Cbj
    Retrieving weather information for saint-denis,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saint-denis%2Cre
    Retrieving weather information for vanimo,pg
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vanimo%2Cpg
    Retrieving weather information for bredasdorp,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bredasdorp%2Cza
    Retrieving weather information for port elizabeth,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=port+elizabeth%2Cza
    Retrieving weather information for kolokani,ml
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kolokani%2Cml
    Retrieving weather information for narbonne,fr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=narbonne%2Cfr
    Retrieving weather information for itarema,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=itarema%2Cbr
    Retrieving weather information for fairbanks,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=fairbanks%2Cus
    Retrieving weather information for kempele,fi
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kempele%2Cfi
    Retrieving weather information for owensboro,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=owensboro%2Cus
    Retrieving weather information for san jose,gt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=san+jose%2Cgt
    Retrieving weather information for gumdag,tm
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=gumdag%2Ctm
    Retrieving weather information for khakhea,bw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=khakhea%2Cbw
    Retrieving weather information for le port,re
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=le+port%2Cre
    Retrieving weather information for rudsar,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=rudsar%2Cir
    Retrieving weather information for charlestown,kn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=charlestown%2Ckn
    Retrieving weather information for inverell,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=inverell%2Cau
    Retrieving weather information for narasannapeta,in
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=narasannapeta%2Cin
    Retrieving weather information for palmer,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=palmer%2Cus
    Retrieving weather information for porto novo,cv
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=porto+novo%2Ccv
    Retrieving weather information for bardiyah,ly
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bardiyah%2Cly
    Retrieving weather information for haputale,lk
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=haputale%2Clk
    Retrieving weather information for uusikaupunki,fi
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=uusikaupunki%2Cfi
    Retrieving weather information for werda,bw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=werda%2Cbw
    Retrieving weather information for chuy,uy
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=chuy%2Cuy
    Retrieving weather information for hay river,ca
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hay+river%2Cca
    Retrieving weather information for apac,ug
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=apac%2Cug
    Retrieving weather information for mhlambanyatsi,sz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mhlambanyatsi%2Csz
    Retrieving weather information for emerald,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=emerald%2Cau
    Retrieving weather information for pandan niog,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=pandan+niog%2Cph
    Retrieving weather information for ajdabiya,ly
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ajdabiya%2Cly
    Retrieving weather information for along,in
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=along%2Cin
    Retrieving weather information for redmond,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=redmond%2Cus
    Retrieving weather information for bulawayo,zw
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bulawayo%2Czw
    Retrieving weather information for atwater,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=atwater%2Cus
    Retrieving weather information for fort wellington,gy
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=fort+wellington%2Cgy
    Retrieving weather information for jardim,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=jardim%2Cbr
    Retrieving weather information for adamas,gr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=adamas%2Cgr
    Retrieving weather information for cairns,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=cairns%2Cau
    Retrieving weather information for northam,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=northam%2Cau
    Retrieving weather information for kumluca,tr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kumluca%2Ctr
    Retrieving weather information for peniche,pt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=peniche%2Cpt
    Retrieving weather information for inirida,co
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=inirida%2Cco
    Retrieving weather information for mason city,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=mason+city%2Cus
    Retrieving weather information for osmena,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=osmena%2Cph
    Retrieving weather information for xixiang,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=xixiang%2Ccn
    Retrieving weather information for wencheng,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=wencheng%2Ccn
    Retrieving weather information for kargasok,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kargasok%2Cru
    Retrieving weather information for serik,tr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=serik%2Ctr
    Retrieving weather information for cravo norte,co
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=cravo+norte%2Cco
    Retrieving weather information for saraza,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saraza%2Cph
    Retrieving weather information for puqi,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=puqi%2Ccn
    Retrieving weather information for winneba,gh
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=winneba%2Cgh
    Retrieving weather information for yellowknife,ca
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=yellowknife%2Cca
    Retrieving weather information for srednekolymsk,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=srednekolymsk%2Cru
    Retrieving weather information for koster,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=koster%2Cza
    Retrieving weather information for moree,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=moree%2Cau
    Retrieving weather information for gorontalo,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=gorontalo%2Cid
    Retrieving weather information for rikitea,pf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=rikitea%2Cpf
    Retrieving weather information for banda aceh,id
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=banda+aceh%2Cid
    Retrieving weather information for varkaus,fi
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=varkaus%2Cfi
    Retrieving weather information for guanare,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=guanare%2Cve
    Retrieving weather information for barbastro,es
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=barbastro%2Ces
    Retrieving weather information for uruguaiana,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=uruguaiana%2Cbr
    Retrieving weather information for charters towers,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=charters+towers%2Cau
    Retrieving weather information for atuona,pf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=atuona%2Cpf
    Retrieving weather information for loukhi,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=loukhi%2Cru
    Retrieving weather information for dembi dolo,et
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=dembi+dolo%2Cet
    Retrieving weather information for longyearbyen,sj
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=longyearbyen%2Csj
    Retrieving weather information for isangel,vu
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=isangel%2Cvu
    Retrieving weather information for cookeville,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=cookeville%2Cus
    Retrieving weather information for hami,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hami%2Ccn
    Retrieving weather information for fereydunshahr,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=fereydunshahr%2Cir
    Retrieving weather information for taltal,cl
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=taltal%2Ccl
    Retrieving weather information for woodward,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=woodward%2Cus
    Retrieving weather information for russell,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=russell%2Cnz
    Retrieving weather information for brae,gb
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=brae%2Cgb
    Retrieving weather information for cehegin,es
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=cehegin%2Ces
    Retrieving weather information for beyneu,kz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=beyneu%2Ckz
    Retrieving weather information for roebourne,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=roebourne%2Cau
    Retrieving weather information for saskylakh,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=saskylakh%2Cru
    Retrieving weather information for roald,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=roald%2Cno
    Retrieving weather information for zemio,cf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=zemio%2Ccf
    Retrieving weather information for bongandanga,cd
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bongandanga%2Ccd
    Retrieving weather information for puerto ayora,ec
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=puerto+ayora%2Cec
    Retrieving weather information for karacakoy,tr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=karacakoy%2Ctr
    Retrieving weather information for nerang,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=nerang%2Cau
    Retrieving weather information for skjervoy,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=skjervoy%2Cno
    Retrieving weather information for paracuru,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=paracuru%2Cbr
    Retrieving weather information for eravur,lk
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=eravur%2Clk
    Retrieving weather information for nakasongola,ug
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=nakasongola%2Cug
    Retrieving weather information for walvis bay,na
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=walvis+bay%2Cna
    Retrieving weather information for linxi,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=linxi%2Ccn
    Retrieving weather information for port hedland,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=port+hedland%2Cau
    Retrieving weather information for paidha,ug
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=paidha%2Cug
    Retrieving weather information for solnechnyy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=solnechnyy%2Cru
    Retrieving weather information for aksu,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=aksu%2Ccn
    Retrieving weather information for puerto escondido,mx
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=puerto+escondido%2Cmx
    Retrieving weather information for bathsheba,bb
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bathsheba%2Cbb
    Retrieving weather information for kalaleh,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kalaleh%2Cir
    Retrieving weather information for naryan-mar,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=naryan-mar%2Cru
    Retrieving weather information for huangzhai,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=huangzhai%2Ccn
    Retrieving weather information for baker city,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=baker+city%2Cus
    Retrieving weather information for deputatskiy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=deputatskiy%2Cru
    Retrieving weather information for garden city,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=garden+city%2Cus
    Retrieving weather information for liloy,ph
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=liloy%2Cph
    Retrieving weather information for beringovskiy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=beringovskiy%2Cru
    Retrieving weather information for victoria point,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=victoria+point%2Cau
    Retrieving weather information for college,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=college%2Cus
    Retrieving weather information for qingdao,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=qingdao%2Ccn
    Retrieving weather information for dunedin,nz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=dunedin%2Cnz
    Retrieving weather information for sibu,my
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=sibu%2Cmy
    Retrieving weather information for barra do bugres,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=barra+do+bugres%2Cbr
    Retrieving weather information for zyryanka,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=zyryanka%2Cru
    Retrieving weather information for marrakesh,ma
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=marrakesh%2Cma
    Retrieving weather information for ciudad bolivar,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ciudad+bolivar%2Cve
    Retrieving weather information for kysyl-syr,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kysyl-syr%2Cru
    Retrieving weather information for seguela,ci
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=seguela%2Cci
    Retrieving weather information for nyurba,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=nyurba%2Cru
    Retrieving weather information for san patricio,mx
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=san+patricio%2Cmx
    Retrieving weather information for hoopstad,za
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hoopstad%2Cza
    Retrieving weather information for po,bf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=po%2Cbf
    Retrieving weather information for jamestown,sh
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=jamestown%2Csh
    Retrieving weather information for maryborough,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=maryborough%2Cau
    Retrieving weather information for binga,cd
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=binga%2Ccd
    Retrieving weather information for belaya gora,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=belaya+gora%2Cru
    Retrieving weather information for stuttgart,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=stuttgart%2Cus
    Retrieving weather information for ivdel,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ivdel%2Cru
    Retrieving weather information for bojnurd,ir
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bojnurd%2Cir
    Retrieving weather information for vuktyl,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vuktyl%2Cru
    Retrieving weather information for byron bay,au
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=byron+bay%2Cau
    Retrieving weather information for hobyo,so
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=hobyo%2Cso
    Retrieving weather information for merced,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=merced%2Cus
    Retrieving weather information for udachnyy,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=udachnyy%2Cru
    Retrieving weather information for iqaluit,ca
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=iqaluit%2Cca
    Retrieving weather information for shihezi,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=shihezi%2Ccn
    Retrieving weather information for thinadhoo,mv
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=thinadhoo%2Cmv
    Retrieving weather information for adrar,dz
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=adrar%2Cdz
    Retrieving weather information for kapit,my
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=kapit%2Cmy
    Retrieving weather information for ponta delgada,pt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=ponta+delgada%2Cpt
    Retrieving weather information for bambous virieux,mu
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=bambous+virieux%2Cmu
    Retrieving weather information for vardo,no
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=vardo%2Cno
    Retrieving weather information for xifeng,cn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=xifeng%2Ccn
    Retrieving weather information for la carolina,es
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=la+carolina%2Ces
    Retrieving weather information for tautira,pf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tautira%2Cpf
    Retrieving weather information for putina,pe
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=putina%2Cpe
    Retrieving weather information for beyla,gn
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=beyla%2Cgn
    Retrieving weather information for tiksi,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tiksi%2Cru
    Retrieving weather information for horta,pt
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=horta%2Cpt
    Retrieving weather information for polunochnoye,ru
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=polunochnoye%2Cru
    Retrieving weather information for harper,lr
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=harper%2Clr
    Retrieving weather information for oildale,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=oildale%2Cus
    Retrieving weather information for coro,ve
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=coro%2Cve
    Retrieving weather information for puno,pe
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=puno%2Cpe
    Retrieving weather information for djougou,bj
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=djougou%2Cbj
    Retrieving weather information for dodge city,us
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=dodge+city%2Cus
    Retrieving weather information for arraial do cabo,br
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=arraial+do+cabo%2Cbr
    Retrieving weather information for tougan,bf
    http://api.openweathermap.org/data/2.5/weather?appid=e2cadc4738a07b7a733b584b14473c3b&units=metric&q=tougan%2Cbf



```python
# Drop cities with missing information
select_cities = select_cities.dropna()
#Export to csv file
select_cities.to_csv("city_weather_data.csv")
```


```python
# Plot settings
def set_plot_prop(x_title,x_lim,y_title):
    plt.title(f"{y_title} vs {x_title}")
    plt.ylabel(y_title)
    plt.xlabel(x_title)
    plt.grid(True)
    plt.xlim(x_lim)
```

# Temperature versus Latitude


```python
# Temperature vs Latitude 
selected_cities.plot(kind="scatter",x="Latitude",y="Temperature")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a1b0d0dd8>




```python
#Set Latitude range at -90,90
#Create bold axis line at 0 Latitude
#Save figure as png
set_plot_prop("Latitude",[-90,90],"Temperature (Celsius)")
plt.axvline(0, color='black')
plt.savefig("Temperature vs Latitude")
plt.show()


```


![png](output_12_0.png)


# Humidity and Latitude


```python
# Humidity vs Latitude 
selected_cities.plot(kind="scatter",x="Latitude",y="Humidity",grid=True)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a18cae0b8>




```python
set_plot_prop("Latitude",[-90,90],"Humidity")
plt.axvline(0, color='black')
plt.savefig("Humidity vs Latitude")
plt.show()
```


![png](output_15_0.png)


# Windspeed with Latitude


```python
# Wind speed vs Latitude
selected_cities["Wind speed"] = pd.to_numeric(selected_cities["Wind speed"])
selected_cities.plot(kind="scatter",x="Latitude",y="Wind speed",grid=True)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a1af466a0>




```python
set_plot_prop("Latitude",[-90,90],"Wind speed (mph)")
plt.axvline(0, color='black')
plt.savefig("Wind Speed vs Latitude")
plt.show()
```


![png](output_18_0.png)


# Cloudiness with Latitude


```python
# Cloudiness vs Latitude
selected_cities["Cloudiness"] = pd.to_numeric(selected_cities["Cloudiness"])
selected_cities.plot(kind="scatter",x="Latitude",y="Cloudiness",grid=True)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1a1b3a1fd0>




```python
set_plot_prop("Latitude",[-90,90],"Cloudiness")
plt.axvline(0, color='black')
plt.savefig("Cloudiness vs Latitude")
plt.show()
```


![png](output_21_0.png)


# Temperature with Latitude and Longitude


```python
# Temperature approaching equator
size_temp = np.round((((selected_cities.Temperature)/100)**2)*10,2)

selected_cities.plot(kind="scatter",x="Longitude",y="Latitude",grid=True, edgecolor='black', 
                     c=size_temp,s=100,cmap=plt.cm.OrRd)
plt.xlabel("Longitude")
plt.tight_layout()
plt.title("Temperature along Longitude and Latitude")
plt.ylabel("Latitude")
plt.ylim([-90,90])
plt.grid(True)

plt.xlim([-200,200])
plt.subplots_adjust(bottom=.25, left=.25)

plt.axhline(0, color='black')
plt.savefig("Temperature along Longitude and Latitude")

plt.show()
```


![png](output_23_0.png)

