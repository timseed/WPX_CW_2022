# CW WPX 2022 

The contest is over, and after a couple of weeks we got the initial raw scores.

Whilst I was happy with my score, I was surprised as to how well some of the other DU stations had performed. 

Please do not mis-take my surprise for jealousy or anything negative, I am truely happy how some of these stations have managed such good scores. I just wonder **how** ?? 

# Background 

The RBN offers a data download - so I grabbed the files from their site. 

[May 28](http://www.reversebeacon.net/raw_data/dl.php?f=20220528)

[May 29](http://www.reversebeacon.net/raw_data/dl.php?f=20220528)

Unzip, and these files are CSV format - so you could process them in Excel (!! - not frankly a good idea, but you could).

These files are joined (cat *.csv > WPX_CW_2022.csv) and then loaded into Pandas.


# Analysis

## Summary

There were 3,900,763 records submitted in this 48 hour period !! That's 22 spots per second - Non-Stop.

## DU Summary

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


For a better understanding - there were also 2 Multi-Band stations operating from Region 3, namely DU3T and 4D3X.

| Power         | Station         | RAW Score     |
|:------------- |:---------------:| -------------:|
| High Power All Band   | DU3T    |  2,776,518    |
| Low Power All Band    | 4D3X    |  1,268,086    |

Both of which look like excellent scores to me.

But, as I was only working on the 10m section, I will focus on this.

# 10M Data

Filtering the data is easy. Just load the CSV files, and place a Band filter - at the same time, we can limit the country to DU by this 


    DU_Spots_10M = contest[(contest.dx_pfx=='DU')&(contest.band=='10m')]

This shows we have 3417 unique spots for DU stations on the 10M band.

## Most Spotted DU Call 

This will show which callsign has been spotted the most. Whilst it is not always the case, being spotted regularly and frequently usually results in you getting a better score. This is why self-spotting is banned !!

```python
du_10m_summary=DU_Spots_10M.groupby(['dx'])['callsign'].count().sort_values(ascending=False).reset_index()
du_10m_summary.columns=['Call','Spotted_Count']
du_10m_summary[du_10m_summary.Spotted_Count>10]
```

Explanation: Group the stations (they are called Dx in the dataset), make a count, rename the column to something more sensible and show those who were spotted more than 10 times.

|    | Call     |   Spotted_Count |
|---:|:---------|----------------:|
|  0 | DU3T     |            2834 |
|  1 | 4D3X     |            1226 |
|  2 | 4F3OM    |            1009 |
|  3 | DU1WBX   |             842 |
|  4 | 4I1EBC   |             789 |
|  5 | DX9EVM   |             515 |
|  6 | DV3A     |             497 |
|  7 | DZ1QN    |             161 |
|  8 | DU1VGX   |             147 |
|  9 | 4E1AGW   |             140 |
| 10 | 4F3BZ    |              99 |
| 11 | DZ9W     |              71 |
| 12 | 4F2KWT   |              59 |
| 13 | N7ET/DU7 |              56 |
| 14 | DU3TT    |              39 |
| 15 | DU1EV    |              31 |
| 16 | DU3TDU   |              15 |
| 17 | 4F1OZ    |              13 |
| 18 | DU8QT    |              12 |

This looks about right, the DU3T (High Power ALl Bands) is at the top, 4D3X (LP All Bands), but then 4F3OM (QRP), 4I1EBC (QRP), DX9EVM (Multi-Single Low-Power),  DV3A (High Power). In 7th place is DZ1QN (All Band QRP).

The other stations I am unaware of their catagory as they are unlisted on the WPX temporary score website.

## Group into HP, LP, QRP

We now Group the data into the claimed power catagories.

This is quite easy to accomplish...The date is rounded to the nearest 5 minutes. Whilst there is some possibilty of propogation changing in that time, the data records which match at a 1 minute time-interval are significantly lower.

```python
DU_QRP = contest[(contest.dx_pfx=='DU')&(contest.band=='10m') & ((contest.dx=='4E1ABC') |(contest.dx=='4F3OM'))]
DU_HP = contest[(contest.dx_pfx=='DU')&(contest.band=='10m') & ((contest.dx=='DU3T') |(contest.dx=='DV3A')|(contest.dx=='DU1EV'))]
DU_LP=contest[(contest.dx_pfx=='DU')&(contest.band=='10m') & ((contest.dx=='4D3X') |(contest.dx=='4E1AGW')|(contest.dx=='4F3BZ'))]
```

So with our HP, LP and QRP Data sets.... we ask the question

**Which skimmmer node heard a HP&QRP station in the same 5 minute window ?** 

Well for HP&QRP there are 153 matching records.

So we now ask... **Did Any QRP Station get received stronger than an HP station in the same time period ?** 

That is expressed like this

```python
HP_vs_QRP=pd.merge(DU_HP,DU_QRP,on=['callsign','date_rounded_5m'],how='inner')[['date_rounded_5m','callsign','freq_x','dx_x','dx_y','db_x','db_y']]
HP_vs_QRP[HP_vs_QRP.db_y >= HP_vs_QRP.db_x]
```

## HP surprise

Now, I would have thought this would yield no records ... but .... 

|     | date_rounded_5m     | callsign   |   freq_x | dx_x   |   freq_y | dx_y   |   db_x |   db_y |
|----:|:--------------------|:-----------|---------:|:-------|---------:|:-------|-------:|-------:|
|  16 | 2022-05-28 08:50:00 | VK6ANC     |  28037.6 | DV3A   |  28019.9 | 4F3OM  |     22 |     25 |
|  36 | 2022-05-28 10:30:00 | VU3KAZ     |  28024.4 | DU3T   |  28032   | 4F3OM  |     21 |     21 |
|  48 | 2022-05-28 10:50:00 | BG4WOM     |  28024.7 | DU3T   |  28032.2 | 4F3OM  |     25 |     31 |
|  49 | 2022-05-28 10:50:00 | BG4WOM     |  28053.5 | DV3A   |  28032.2 | 4F3OM  |     24 |     31 |
|  61 | 2022-05-28 11:00:00 | BD7LAE     |  28024.4 | DU3T   |  28039.9 | 4F3OM  |     32 |     32 |
| 141 | 2022-05-30 06:30:00 | VK4CT      |  28002.1 | DV3A   |  28032.9 | 4F3OM  |     13 |     14 |


I will explain the 1st line.

@2022-05-28 08:50:00, VK6ANC heard on (freq_x) 28037.6 call DV3A at strength (db_x) 22 dB, it also heard on (freq_y) 28019.9 call 4F3OM at strength (db_y) 25 dB.

This is most impressive for a QRP station - look they are over 6 dB stronger going into BG4WOM than DU3T and DV3A.

## LP 

Having seen very poorly configured HP stations, do the LP stations suffer from the same issue.

This is the code

```python
LP_vs_QRP=pd.merge(DU_LP,DU_QRP,on=['callsign','date_rounded_5m'],how='inner')[['date_rounded_5m','callsign','freq_x','dx_x','freq_y','dx_y','db_x','db_y']]
df_to_md(LP_vs_QRP[LP_vs_QRP.db_y >= LP_vs_QRP.db_x])
```
 
|    | date_rounded_5m     | callsign   |   freq_x | dx_x   |   freq_y | dx_y   |   db_x |   db_y |
|---:|:--------------------|:-----------|---------:|:-------|---------:|:-------|-------:|-------:|
| 30 | 2022-05-29 14:00:00 | JH7CSU1    |  28009.2 | 4F3BZ  |  28040.1 | 4F3OM  |     12 |     21 |
| 34 | 2022-05-29 14:30:00 | JA4ZRK     |  28033   | 4F3BZ  |  28001   | 4F3OM  |      3 |      8 |
| 46 | 2022-05-29 16:05:00 | JH7CSU1    |  28034.1 | 4F3BZ  |  28023.6 | 4F3OM  |     18 |     20 |
| 47 | 2022-05-29 16:10:00 | VU3KAZ     |  28034   | 4F3BZ  |  28023.5 | 4F3OM  |     15 |     17 |


Sadly this again shows 4F3BZ has issues in their transmission system compared to the mighty 4F3OM. being over powered by over 7db in some cases !! 

Interestingly 4D3X, was not overmatched - so this operator must be doing something corect.

