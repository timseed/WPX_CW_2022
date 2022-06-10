# CW WPX 2022 

The contest is over, and after a couple of weeks we got the initial raw scores.

Whilst I was happy with my score, I was surprised as to how well some of the other DU stations had performed. 

Please do not mis-take my surprise for jealousy or anything negative, I am truely happy how some of these stations have managed such good scores. I just wonder **how** ?? 

# Background 

As I run my own RBN data processed suite, I record all Spots that I am able to collect during the contest. I generally do not place any filters on the incoming data streams, as I apply my own set of data processing rules/validation before forwarding the Spots I think will be helpful to my own logging software (which I also wrote).

Why is this important to note ? Well, it means that I have a large volume of data that was publically published. This data, is usually available if you write to the Sys-Ops of the specific nodes. For RBN (Reverse Beacon Network) data - this is available in daily dumps from their web site.

So - this data is publially available, and may show some insight.


# ETL

The data is in a little bit of a strange format, as the Telnet feeds allow automated and manual data. But 90% of the records are generally generated from CwSkimmer. 

A Typical line of data looks like this 

    # dxc.ve7cc.net,Low Close,DX de WC2L-#:     7004.1  YT2T         CW  5 dB 32 WPM CQ             2352Z
    
Becuase of my custom data-pipeline I know this means

   - Source
     -  dxc.ve7cc.net
   - What Rule (my software) mached
     -  Low Close
   - Data
     - The **Data Line** Exactly as placed into the Telnet System


### Data Line   

The **Data Line** has quite a large amount of information

    - Who Spotted the call
    - Frequency
    - The Callsign that was Spotted
    - Were they calling Running or Pouncing  (CQ or De)
    - How loud they are (db)
    - Cw Speed (Wpm)

I load and as best as I can, clean this data up. 

Then I augment the data by calculating the following (more can be added, but this seems enough for the time being).

  - Spotter Country
  - Spotter Continent
  - Band (Convert khz to Band)
  - Call Country
  - Call Continent
  
  
This data can be calculated using CTY.DAT published [
CTY.DAT Format - Amateur Radio Country Fileshttps://www.country-files.com ](
CTY.DAT Format - Amateur Radio Country Fileshttps://www.country-files.com ), and I have my own custom code to read this.

With all the records enhanced I then remove the duplicates. Due to how the Telnet clusters are configured, aprox 4% of all 'Spots' are duplicates. So it is best to remove these.

At this stage the ETL (Extract Translate and Load) phase is complete. So I save the output as a dataset (in Python this is called a Pickle File).

## Analysis

Loading the pickle file from before, I do some inital high level analysis, using data published at [https://www.cqwpx.com/raw.htm?mode=cw&reg=DU&fbclid=IwAR2vneBY2AFg_rnfxGB3vYtTBYjRDS6qEivrP_X-mh99OxF7aPXSdwQVCJY](https://www.cqwpx.com/raw.htm?mode=cw&reg=DU&fbclid=IwAR2vneBY2AFg_rnfxGB3vYtTBYjRDS6qEivrP_X-mh99OxF7aPXSdwQVCJY)

What caught my interest was my score, compared to the Low Power and the QRP - frankly there was not much difference in the scores.

| Power         | Station         | RAW Score     |
|:------------- |:---------------:| -------------:|
| High 10M      | DV3A            |      396,180  |
| High 10M      | DU1EV           |      302,649  |
| Low 10M       | 4F3BZ           |      341,497  |
| QRP 10M       | 4I1EBC          |       290,180 |
| QRP 10M       | 4F3OM           |       164,099 |


The QRP and the Low-Power guys are obviously doing a fantastic job.

Just to add a reference - I then looked at the top Scores in Oceania - as the DU stations have all the top spots - these are 3'rd place for each of the 'Oceania division'

| Power         | Station         | RAW Score     |
|:------------- |:---------------:| -------------:|
| High 10M      | ZL3P            |  156,780      |
| Low 10M       | YD2UWF          | 111,510       |
| QRP 10M       | YB3BOA          |   5,616       |

### What's the big picture ?

How many spots did the DU HP stations get ??

| Call    | Spots |
|---------|-------|
| DV3A    | 79    |
| DU1EV   | 12    |

Big difference, I am wondering if this is due to my own skimmer ?? [Which I have deliberatly tried to prevent from spotting myself]

#### DV3A Spots by Country

|    | SpotterCountry       |   when |
|---:|:---------------------|-------:|
|  0 | Australia            |      2 |
|  1 | Brazil               |      1 |
|  2 | China                |     32 |
|  3 | Czech Republic       |      2 |
|  4 | Fed. Rep. of Germany |      4 |
|  5 | Hungary              |      2 |
|  6 | India                |      9 |
|  7 | Japan                |     19 |
|  8 | Namibia              |      2 |
|  9 | Republic of Korea    |      1 |
| 10 | Samoa                |      4 |
| 11 | Thailand             |      1 |

That looks ok.

#### DU1EV Spots by Country

|    | SpotterCountry       |   when |
|---:|:---------------------|-------:|
|  0 | Fed. Rep. of Germany |      3 |
|  1 | Japan                |      4 |
|  2 | Kazakhstan           |      2 |
|  3 | Lithuania            |      1 |
|  4 | Slovenia             |      1 |
|  5 | Spain                |      1 |


That seems strange - no JA, no BV - maybe my rules are at fault here.



## Low Power 

There is only 1 LP 10M only station, 4F3BZ. Nice station, and operator; I often hear them in the background (like most of the DU1 stations they are in my dead zone).


#### 4F3BZ Spots by Country

| SpotterCountry   |   Country |
|:-----------------|----------:|
| China            |        10 |
| Japan            |         9 |
| India            |         5 |
| Bulgaria         |         2 |
| Canary Islands   |         1 |
| Lithuania        |         1 |
| Romania          |         1 |
| Samoa            |         1 |
| West Malaysia    |         1 |


## QRP Power

There are two most excellent DU QRP stations listed in the RAW scores... so let us check them.

#### 4I1EBC Spots by Country

Very impressive, if I were 4F3BZ or DU1EV I would be very jealous of those spots. Canary Islands is particularly impressive for a QRP station, especially in contest conditions. Well done. 

|    | SpotterCountry       |   when |
|---:|:---------------------|-------:|
|  0 | Australia            |      9 |
|  1 | Bulgaria             |      1 |
|  2 | Canary Islands       |      1 |
|  3 | China                |     80 |
|  4 | Denmark              |      1 |
|  5 | European Russia      |      1 |
|  6 | Fed. Rep. of Germany |      1 |
|  7 | India                |     15 |
|  8 | Japan                |     34 |
|  9 | Lithuania            |      1 |
| 10 | Mauritius            |      1 |
| 11 | Thailand             |      3 |

#### 4F3OM Spots by Country

|    | SpotterCountry   |   when |
|---:|:-----------------|-------:|
|  0 | Australia        |      2 |
|  1 | China            |     97 |
|  2 | Czech Republic   |      1 |
|  3 | European Russia  |      1 |
|  4 | India            |      7 |
|  5 | Japan            |     38 |
|  6 | Philippines      |      1 |

More China than the other QRP station, but less EU. Still very impressive to get into EU on QRP during a contest.


#### YB3BOA Spots by country

|    | SpotterCountry       |   when |
|---:|:---------------------|-------:|
|  0 | Australia            |      3 |
|  1 | China                |      7 |
|  2 | Fed. Rep. of Germany |      1 |
|  3 | India                |      3 |
|  4 | Japan                |      6 |
|  5 | Thailand             |      2 |

Amazing there are only 2 Thai spots (maybe no Skimmers there), but 1 spot into Germany is excellent.


#Compare by power.

Sadly at the moment, there is no way of creating a benchmark of receiver station signals. There could be different antenna, pre-amps, coax etc... 

As the **Data Line** (explained earlier) shows RxDb - lets change the queries to see how loud some stations are.

My own station runs the following

  - 400W
  - Antennas
    - 4 Element Yagi
      - Elevation 20m 
    - 5 Element Yagi 
      - Elevation 21m
 
I suspect my antenna has a gain of aprox 5.85dB - so my 400W input should be an ERP of aprox 1100W of an isotropic antenna.


### DV3A Country by Max Received Signal

|    | SpotterCountry       |   Band |   sigdb |
|---:|:---------------------|-------:|--------:|
|  0 | India                |     10 |      48 |
|  1 | China                |     10 |      40 |
|  2 | Japan                |     10 |      33 |
|  3 | Fed. Rep. of Germany |     10 |      24 |
|  4 | Hungary              |     10 |      20 |
|  5 | Australia            |     10 |      16 |
|  6 | Samoa                |     10 |       9 |
|  7 | Czech Republic       |     10 |       8 |
|  8 | Namibia              |     10 |       3 |
|  9 | Republic of Korea    |     10 |       0 |
| 10 | Brazil               |     10 |      -1 |

I suspect the Indian station has a pre-amp, as working VU is quite tricky. But China, Germany were all good copies, Brazil being very difficult this time around (-1 indicates a Manual spot - with no signal strength report).

### DU1EV Country by Max Received Signal

|    | SpotterCountry       |   Band |   sigdb |
|---:|:---------------------|-------:|--------:|
|  0 | Fed. Rep. of Germany |     10 |      21 |
|  1 | Japan                |     10 |       0 |
|  2 | Kazakhstan           |     10 |       0 |
|  3 | Lithuania            |     10 |       0 |
|  4 | Slovenia             |     10 |       0 |
|  5 | Spain                |     10 |      -1 |

Strange, only 1 automated Spot from Germany. 

Sadly this tells me little - unless the operator was doing S&P all through the contest !

### Low Power 

### 4F3BZ Country by Max Received Signal

| SpotterCountry   |   Band |   sigdb |
|---:|:-----------------|-------:|--------:|
|  0 | India            |     10 |      48 |
|  1 | China            |     10 |      25 |
|  2 | Japan            |     10 |      18 |
|  3 | Canary Islands   |     10 |       7 |
|  4 | Samoa            |     10 |       7 |
|  5 | West Malaysia    |     10 |       4 |
|  6 | Bulgaria         |     10 |       0 |
|  7 | Lithuania        |     10 |       0 |
|  8 | Romania          |     10 |      -1 |

Again I think we need to discount India. But China, Japan are good and strong. 

DV3A was 40Db into China, 4F3BZ was 25. I was expecting a drop of 6Db (400W to 100W).... assuming antennas, coax etc are the same.

DV3A was 33Db into Japan, 4F3BZ was 18Db. - expected this to be 27dB.


### QRP 

### 4I1EB Country by Max Received Signal

|    | SpotterCountry       |   Band |   sigdb |
|---:|:---------------------|-------:|--------:|
|  0 | India                |     10 |      42 |
|  1 | China                |     10 |      38 |
|  2 | Japan                |     10 |      29 |
|  3 | European Russia      |     10 |      21 |
|  4 | Australia            |     10 |      14 |
|  5 | Canary Islands       |     10 |       4 |
|  6 | Mauritius            |     10 |       4 |
|  7 | Bulgaria             |     10 |       0 |
|  8 | Denmark              |     10 |       0 |
|  9 | Fed. Rep. of Germany |     10 |       0 |
| 10 | Lithuania            |     10 |       0 |
| 11 | Thailand             |     10 |      -1 |

 Amazing figures - these numbers for China, Japan are just 3Db down from the HP 10M station DV3A.
 
 
### 4F3OM Country by Max Received Signal
 

|    | SpotterCountry   |   Band |   sigdb |
|---:|:-----------------|-------:|--------:|
|  0 | India            |     10 |      31 |
|  1 | China            |     10 |      30 |
|  2 | Japan            |     10 |      19 |
|  3 | Australia        |     10 |      12 |
|  4 | European Russia  |     10 |       5 |
|  5 | Czech Republic   |     10 |       4 |
|  6 | Philippines      |     10 |       0 |

Also amazingly good signals. 30Db into China, 19Db into Japan; Stronger than the low-powered station.



# Conclusions

Some amazing stations here in DU Land, especially those QRP. I do hope they publish some data on their setup, receiving systems etc; It would be very interesting reading.

A Rx measuring project similar to DXCC beacons would be most useful, as would knowing what receiving antenna configurations the stations that are generating the Dx Spots possess.

As I am a member of the RBN Group, I will make enquiries - I am sure this data is already known, and some stations may well caliberate their Rx levels already.

*tempus breve est* 

