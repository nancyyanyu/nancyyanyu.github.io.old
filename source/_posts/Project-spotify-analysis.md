---
title: 'Data Analysis of K-POP: Playing with Spotify API'
categories: Project
mathjax: true
comments: true
abbrlink: 63adf3bb
tags: 
- AWS
- Airflow
- Visualization
date: 2019/10/19
hidden: true
---





# Introduction

K-pop has become a phenomenon in the U.S, as evidenced by bombing number of K-pop shows across the nation. In Spotify's K-pop genre, there are more than 500 K-pop artists. Among them, the wildly popular male group BTS is certainly worth mentioning. According to the data of Top 100 selling artists in the first half of 2019, out of 10 album copies sold out, there are 4 of BTS's albums [(Ref)](https://www.allkpop.com/article/2019/07/bts-occupies-419-of-total-album-sales-of-top-100-kpop-artists). 

BTS is certainly a reason for the rise of K-pop, but as more and more K-pop artists successfully hit the Billboard charts and took massive world tour, K-pop is definitely a hot topic to dig into.

This K-Pop sensation draws my attention to perform data analysis using data provided by ***Spotify API*** which could be used to answer some interesting questions like:

- Besides BTS, who are the best K-pop artists in the U.S. market? 
- How do their popularity change? 
- How did the market of K-pop music evolve in size and audio features?

<!--more-->



# Setup

### **Register an app**:

Just like my last project which used Twitter API, the first step is to register an app on [Spotify for Developers](https://developer.spotify.com/), then copy *Client ID* and *Client Secret*.

<img src="./1.jpg" width="800" />

&nbsp; 



### **Install Spotify Web API Python library:**

```bash
pip install spotipy
```



Please check ***spotipy***'s [document](https://spotipy.readthedocs.io/en/latest/) for more detail.

&nbsp; 



### Download all relevant data to local machine

I firstly wrote a class ***MySpotify*** to grab all relevant data and stored the data under local machine in *json* format.

#### **Relevant data:**

- All artists' information under 'k-pop' genre;
- All albums' information of every k-pop artists
- All tracks' information of all albums of every k-pop artists
- Audio features provided by *Spotify* of all tracks

#### **Audio features:**

There are 8 main audio features provided by *Spotify*. Here's the explanation from Spotify's [document](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/).

- ***Danceability*** describes how suitable a track is for dancing based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity. 
- **Energy** is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity. 
- **Instrumentalness** predicts whether a track contains no vocals.  
- **Liveness** detects the presence of an audience in the recording. 
- **Loudness**: the overall loudness of a track in decibels (dB). 
- **Speechiness** detects the presence of spoken words in a track. 
- **Valence:** A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track. 
- **Tempo:** the overall estimated tempo of a track in beats per minute (BPM). 



```python
class MySpotify(object):
    """This class was to download data from Spotify API.
    The download process should be: 
        artist=artist_genre -> album=album_artists(artist) -> songs=songs_albums(album)
    
    """
    def __init__(self,genre='k-pop'):
        self.authenticate()
        self.genre=genre
                
    def authenticate(self):
        auth=open_json('auth')
        my_id = auth['my_id']
        secret_key = auth['secret_key']
        
        client_credentials_manager = SpotifyClientCredentials(client_id = my_id, client_secret = secret_key)
        self.sp = spotipy.Spotify(client_credentials_manager=client_credentials_manager)                
        
......
```

[Full code](https://github.com/nancyyanyu/mini_projects/blob/master/spotify_project/spotify_app.py)

&nbsp;



# Data Analysis

### 1. Who are the most popular K-pop artists?

Before we jump to the answer, let me take a second to introduce two measures of prevelance in Spotify.

- **Followers:** The number of people following the artist.
- **Popularity:** The popularity of the artist. The value will be between 0 and 100, with 100 being the most popular. The artist’s popularity is calculated from the popularity of all the artist’s tracks.

There are cases when some artists have a large amount of followers, but relatively low popularity. That's possibly because they are not active in releasing new songs recently or simply retired from musical life.



To find the hottest K-pop artists, I extracted all artists' followers and popularity data, ranked them, and visualized top 40 artists in terms of the number of followers.

<img src="./2.png" alt="Fig 1" width="800" />

In *Fig 1*, for artists with <font color="orange">orange</font> bar higher than <font color="blue">blue</font> bar, some of them are hot, recently debuted K-pop groups, like ***(G)I-DLE, NCT 127, Stray Kids***; most of them released new songs in recent month, e.g.  **Jay Park** released his new EP "Nothing Matters" this month.

For artists with <font color="orange">orange</font> bar lower than <font color="blue">blue</font> bar, some of them are inactive for a long time, such as **G-Dragon** who is serving compulsory military duty; some of them are *temporary* group or solo which only released one or two albums, like  **EXO-CBX, J-hope**. 



Ranking could be sometimes misleading. As 4 out of 10 album copies sold out are BTS's albums, what if this K-pop phenomenon is actually BTS phenomenon? Let's check the numerical number of followers and popularity of these K-pop artists.

<img src="./3.png" alt="Fig 2" width="800" />

<img src="./4.png" alt="Fig 3" width="800" />



From *Fig 2 & 3*, we can see that BTS has more than 1/3 followers than the second hottest group, and more than 1/2 followers than the third one. In addition, BTS has an popularity rate of 99, very close to 100, the highest possible popularity rate. 

For comparison, I listed some top artists in the U.S. market:

| Artists       | Followers  | Popularity |
| ------------- | ---------- | ---------- |
| Ed Sheeran    | 47,295,649 | 100        |
| Drake         | 38,673,834 | 99         |
| Taylor Swift  | 21,198,450 | 93         |
| Ariana Grande | 32,355,391 | 95         |



Comparing the top K-pop artists and the top U.S. mainstream artists, except from BTS, other K-pop artists' could hardly be labelled as top stars in the U.S. Even BTS who have amazingly high popularity, they have significantly less followers. 

In my point of view, before BTS rising to fame, K-pop is only a subculture in the U.S., and not usually depicted by western mainstream media. Today, whether K-pop has become a mainstream phenomenon is still open to debate. At least the far less followers of most of the K-pop artists is an evidence against this argument.



### 2. How does popularity change?

Since *Spotify* does not provide historical data of artists' popularity and followers, I write a class ***Popularity*** to  grab everyday's data, upload to AWS DynamoDB.

```python
class Popularity(MySpotify):    
    def __init__(self,table_name='KPOP_POPULARITY',genre='k-pop'):
        super().__init__(genre)
        self.table = dynamodb.Table(table_name)
        self.track_table=dynamodb.Table(table_name+'_TRACK')
        self.data_folder='%s_artists_info'%self.genre
        self.today=str(dt.datetime.now().date())
        
    def put_data(self):
        artists=open_json("data/artists/"+self.data_folder)            
        info={}
        for artist in artists:
            info[artist['name']]={}
            info[artist['name']]['followers']=artist['followers']['total']
            info[artist['name']]['popularity']=artist['popularity']
        
        self.table.put_item(
           Item={'time': self.today,
               'info':info})
        print("Update artist data to AWS DynamoDB: {}".format(self.today))
                
......
```

[Full code](https://github.com/nancyyanyu/mini_projects/blob/master/spotify_project/popularity.py)



The ***ETL process*** works like this:

<center><img src="./6.png" width="540" /></center>
1. Grab artists' data from *Spotify* API;
2. Organize and clean the data;
3. Save data to local machine in *json* format;
4. Save data to **AWS DynamoDB**. 



Here is a visualization of the change of BTS' followers & popularity using data collected during writing this report.

<img src="./5.png" alt="Fig 4" width="800" />





### 3. How many K-pop albums released every year?

As an industry, K-pop has experienced significant increase in size over the past two decades. According to each music agent companies' marketing tactics, K-pop artitists will demonstrate certain pattern in terms of the timing of releasing new albums. 

Here is a visualization of the number of albums released by year and by month. We can see a sharp increase from 2016 to 2018. Also artists seem to be more active during October and November. 

<img src="./7.png" width="700" />





### 4. Audios' features analysis

To analyze audio features, I wrote a class ***Analyze*** to wrap to ETL process and analysis functions.

```python
class Analyze(object):       
    def __init__(self,genre='k-pop'):
        self.features=['acousticness','liveness',          
                  'instrumentalness','speechiness', 
                   'danceability','energy','valence','tempo']
        self.poplr=Popularity()
    
    def extract_data(self,artist):
        df=pd.read_hdf('./data/analysis/{}_tracks_analysis.h5'.format(artist))
        return df
......
```

[Full code](https://github.com/nancyyanyu/mini_projects/blob/master/spotify_project/track_analysis.py)



The ***ETL process*** works like this:



<center><img src="./10.png" width="540" /></center>


As last chapter says, I extracted 8 audio features of all tracks. I calculated the truncated average of each feature by year, and try to find some changes in the K-pop music itself.



<img src="./8.png" width="800" />



We can see features like ***acousticness***, and ***instrumentalness*** have an upward trend; while ***energy***, ***tempo*** and ***valence*** have un downward trend in recent years. 

The evolution of K-pop music is not smooth. It seems it has experienced a radical shift around 2013 (2012-2014), as most of the features changed their disposition. After 2013 or 2014, the K-pop songs:

- Become more and more acoustic, and instrumental. 
- Contain more spoken words. 
- Convey less energy and positiveness. In other words, they sound less cheerful but more sad and depressed. 
- Become a bit slower in tempo. 
- Are still highly danceable, but not as danceable as before 2014.



