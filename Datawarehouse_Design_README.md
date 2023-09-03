# SpotifyETL
Data warehouse design : https://docs.google.com/document/d/1Fg9w5fB-hpuxhhk5gGdLDB7AJYX6Vp-olmubNE8UvNY/edit?usp=sharing
Business process
The Data warehouse is designed for Song listening business process. This process is described in the document Requirements specification for Song listening business process.
Schema

Entity set description



User[D]
A majority of the data comes from a database that is automatically filled with data from user actions. It stores user-oriented data such as user identification details, favorite songs, user-created playlists, suggested playlists, and time logs: daily app use time 
Name
Primary key
Type/Domain
Description
UserID
Yes
INT
Unique user ID
username
No
VARCHAR
name chosen by user
email
No
VARCHAR
user email address
id_Favorite_song
No
INT
Favorite user song




Song[SCD]
Spotify also has a second database that contains data about songs. The database should contain song-oriented data such as labels, publication dates, and writers/producers.
Name
Primary key
Type/Domain
Description
SongID
Yes
INT
Unique song ID
SongName
No
VARCHAR
Name of the song
id_PublicationDate
No
INT
Date that song was published
Artist
No
VARCHAR
Artist who performs the song
Writer
No
VARCHAR
Writer who wrote the song
Producer
No
VARCHAR
Producer who produced the song
Genre
No
VARCHAR
Genre of the song. Allowed values are listed on this website: Music Genre List - A complete list of music styles, types and genres (musicgenreslist.com) 
Streams
No
VARCHAR
Range of number of times a song has been played  LITTLE for 0 - 1000, NORMAL for 1001- 5000, A LOT for 5001+
rankCategory
No
VARCHAR
rank category of a song, updated monthly. Allowed values: from 1 to 20, from 21 to 50, from 51  to  70, from 71 to 100, more than 100
Id_Label
No
INT
Label of the artist
currentFlag
No
BIT
flag indicating the current version. Allowed values True for current, False  for old data
SID
No
VARCHAR
Identification Number of a specific song




Label[D]
Spotify has a database that contains label data, which includes the label name, the contract length signed with Spotify, the number of songs, the number of artists, and the main genre of music.
Name
Primary key
Type/Domain
Description
id
Yes
INT
Unique Label ID
LabelName
No
VARCHAR
Name of the label
MainGenre
No
VARCHAR
The genre of the most number of songs that a label has. Allowed values are listed on this website: Music Genre List - A complete list of music styles, types and genres (musicgenreslist.com) 
NumberOfSongs
No
VARCHAR
Range of number of songs a label owns LITTLE for 0 - 1000, NORMAL for 1001- 5000, A LOT for 5001+
Id_CreationDate
No
INT
The date that the label was founded
Id_ContractStart
No
INT
The beginning date of the contract
Id_ContractEnd
No
INT
The end date of the contract
NumberOfArtists
No
VARCHAR
Total number of artists signed to the label  LITTLE for 0 - 100, NORMAL for 101- 500, A LOT for 501+
CostOFContract
No
VARCHAR
Cost in ranges CHEAP for 0-10000, NORMAL for 10000-50000 and EXPENSIVE for 50000+




TimeLog [F]
Log of daily app user session 
Name
Primary key
Type/Domain
Description
id
Yes
INT
Unique Log ID
UserID
Yes
INT
Unique user ID
id_date_SessionStart
Yes
INT
Precise time when user started using the app
id_date_SessionEnd
Yes
INT
Precise time when user stopped using the app
TimeSpent
No
INT
Amount of time spent by a user on the app in minutes
id_time_SessionStart
Yes
INT
id of user session time start
id_time_SessionEnd
Yes
INT
id of user session time end
Num_of_songs
No
INT
Number of songs listened to in a session
Num_of_artists
No
INT
Number of artists listened to in a session
TimeSpent_Choosing_Songs
No
INT
Time in minutes spent choosing songs in a session




SongLog [F]
Log of time when a user chose to listen to a specific song and time  when the user stopped listening to the song
Name
Primary key
Type/Domain
Description
SongID
Yes
INT
Unique song ID
UserID
Yes
INT
Unique user ID
id_StartTime
Yes
INT
Precise time of playing the song by a user
id_EndTimeStamp
Yes
INT
Precise time the user stops listening to the song
id_date_startTime
Yes
INT
Date of playing the song
id_date_endTime
Yes
INT
Date of stopping playing the song
Listening_percentage
No
INT
Percentage recorded in the case when a song was stopped before end 0-100
AI_suggested [DD]
Yes
INT
Type search = 0, AI-suggestions = 1 Degenerate dimension
Time_Listened_To
No
INT
Time spent listening to a song in minutes
isFavoriteSong
No
INT
IF song is favorite = 1, else = 0
DurationOfSong
No
INT
Duration of song in Minutes
isFavoriteAndAIsuggestedSong
No
INT
IF song is favorite and was AI-suggested to user = 1, else = 0  
StoppedListening
No
INT
If song was stopped during listening = 1, else = 0  




Time[D]
Time represented in the 24 hour format
Name
Primary key
Type/Domain
Description
id
Yes
INT
id of time
hour
No
INT
hour of local time 0 - 23
minutes
No
INT
minute of local time 0 - 59
seconds
No
INT
seconds of local time 0-59




Date [D]
Local date according to the gregorian calendar
Name
Primary key
Type/Domain
Description
id
Yes
INT
id of date
Day
No
INT
date of the month 01 - 31
Month
No
VARCHAR
name of month of the year Allowed values: January, February,  March, April, May, June,  July, August,  September, October,  November and  December.
Year
No
INT
year in gregorian calendar
















































Dimensional model
Fact definitions
Fact 1 Song listening fact: Listening to a song found by a specific choice method, by a specific user, for a specific time period, of a specific genre from a specific label

Fact Table: SongLog

Granularity:
a specified start time of listening
a specified end time of listening
a specified user
a specified method of finding the song
a specified genre
a specified label

Measures and aggregation functions:
Number of song listening facts COUNT(1)
Time spent listening to songs SUM(Time_Listened_To)  
Number of AI-suggested songs played to users SUM(Al_suggested)
Number of songs stopped by users SUM(StoppedListening)
Number of AI-suggested songs marked as favorite SUM(isFavoriteAndAIsuggestedSong)
 

Fact 2 Time log by user fact: Session of  a specific user spending time on the app during specified day, at a specified time.
Fact resulting from
Fact table: TimeLog

Granularity:
a specified user
a specified time of starting session
a specified time of ending session
a specified date of starting session
a specified date of ending session

Measures and aggregation functions:
Average time spent AVG(TimeSpent)
Number of artists SUM(Num_of_artists)
Number of songs SUM(Num_of_songs)
Average time spent choosing songs AVG(Time_spent_choosing_songs)


Dimension definitions
Dimensions 1 Song listening fact:

DIMENSION/DIMENSION ATTRIBUTE
TABLE/COLUMN
TYPE
AI-Suggested
SongLog.AI_Suggested
Degenerate Dimension
Song
Song
Dimension
SongName
Song.SongName
Dimension attribute
Artist
Song.Artist
Dimension attribute
Writer
Song.Writer
Dimension attribute
Producer
Song.Producer
Dimension attribute
Genre
Song.Genre
Dimension attribute
Streams
Song.Streams
Dimension attribute
rankCategory
Song.rankCategory
Dimension attribute
Song Hierarchy
+ Song.Genre
++ Song.SongName
Hierarchy Dimension
PublicationDate
Date
Dimension
Publication Day
Date.Day
Dimension attribute
Publication Month
Date.Month
Dimension attribute
Publication Year
Date.Year
Dimension attribute
PublicationDate Hierarchy
+ Date.Year
++ Date.Month
+++ Date.Day
Hierarchy Dimension
Label
Label
Dimension
LabelName
Label.LabelName
Dimension attribute
MainGenre
Label.MainGenre
Dimension attribute
NumberOfSongs
Label.NumberOfSongs
Dimension attribute
NumberOfArtists
Label.NumberOfArtists
Dimension attribute
CostOfContract
Label.CostOfContract
Dimension attribute
CreationDate
Date
Dimension
Creation Day
Date.Day
Dimension attribute
Creation Month
Date.Month
Dimension attribute
Creation Year
Date.Year
Dimension attribute
CreationDate Hierarchy
+ Date.Year
++ Date.Month
+++ Date.Day
Hierarchy Dimension
ContractStartDate
Date
Dimension
ContractStart Day
Date.Day
Dimension attribute
ContractStart Month
Date.Month
Dimension attribute
ContractStart Year
Date.Year
Dimension attribute
ContractStartDate Hierarchy
+ Date.Year
++ Date.Month
+++ Date.Day
Hierarchy Dimension
ContractEndDate
Date
Dimension
ContractEnd Day
Date.Day
Dimension attribute
ContractEnd Month
Date.Month
Dimension attribute
ContractEnd Year
Date.Year
Dimension attribute
ContractEndDate Hierarchy
+ Date.Year
++ Date.Month
+++ Date.Day
Hierarchy Dimension
User
User
Dimension
Username
User.Username
Dimension attribute
Email
User.Email
Dimension attribute
age
User.age
Dimension attribute


Dimensions for Time log fact:

DIMENSION/DIMENSION ATTRIBUTE
TABLE/COLUMN
TYPE
SessionStartTime
Time
Dimension
Session Start Hour
Time.Hour
Dimension attribute
Session Start Minute
Time.Minute
Dimension attribute
SessionStartTime Hierarchy
+ Time.Hour
++ Time.Minute
Hierarchy Dimension
SessionEndTime
Time
Dimension
Session End Hour
Time.Hour
Dimension attribute
Session End Minute
Time.Minute
Dimension attribute
SessionEndTime Hierarchy
+ Time.Hour
++ Time.Minute
Hierarchy Dimension
SessionStartDate
Date
Dimension
Session Start Day
Date.Day
Dimension attribute
Session Start Month
Date.Month
Dimension attribute
Session Start  Year                                           
Date.Year
Dimension attribute
SessionStartDate Hierarchy
+ Date.Year
++ Date.Month
+++ Date.Day
Hierarchy Dimension
SessionEndDate
Date
Dimension
Session End Day
Date.Day
Dimension attribute
Session End Month
Date.Month
Dimension attribute
Session End  Year                                       
Date.Year
Dimension attribute
SessionEndDate Hierarchy
+ Date.Year
++ Date.Month
+++ Date.Day
Hierarchy Dimension
User
User
Dimension
Username
User.Username
Dimension attribute
Email
User.Email
Dimension attribute
age
User.age
Dimension attribute


Checking the feasibility of queries based on the multidimensional model
Compare the amount of time spent listening to different genres in the last 3 months.
Measure: Time spent listening to songs,
Dimension: Date (dimension attributes: month, year)
Dimension: Song (dimension attributes: Genre)

How much time by a user is averagely spent on choosing a song?
Measure: Average time spent choosing songs

How long during a day an average user listens to songs recommended by the AI?
Measure: Time spent listening to song
Dimension: Date (dimension attributes: day, month, year)
Degenerate Dimension: AI_Suggested

From which label users spend the most time listening to their music on an average day?
Measure: Time spent listening to songs
Dimension: Song (dimension attributes: Label)
Dimension: Date (dimension attributes: day, month, year)

To how many different artists does a user listen to on a daily average?
Measure: number of artists user listens to
Dimension: User: (dimension attributes: username)
Dimension: Date (dimension attributes: day, month, year)

How many artists on the charts does a user listen to during the day?
Measure: number of artists user listens to
Dimension: Date (dimension attributes:  day, month, year)
Dimension: User: (dimension attributes: username)
Dimension: Song (dimension attribute: rankCategory, currentFlag)

How long during a day an average user listens to songs from charts?
Measure: time spent listening to songs
Dimension: Date (dimension attributes: day, month, year)
Dimension: Song(dimension attribute: rankCategory, currentFlag)

Are songs that are recommended stopped more than those chosen by the users?
Measure: number of songs stopped by user
Degenerated dimension: AI-suggested

How many of the songs that have “A LOT” of streams were suggested to users in the past month?
Measure: number of AI-suggested songs
Dimension: Date (dimension attributes: month, year)
Dimension: Song (dimension attributes: Streams )
Degenerate Dimension: AI_Suggested

Are the recommended songs marked as favorites more often than the chosen ones?
Measure: number of AI-suggested songs marked as favorite

How many not-stopped AI-suggested songs the user listen to during the day (on average)?
Measure: number of songs stopped by user
Dimension: Date (dimension attributes: day, month, year)

From which label were songs most commonly suggested?
Measure:  number of songs AI-suggested to users
Dimension: Label (dimension attributes: labelName)

Compare the ranking of 10 most commonly recommended songs with chart data.
Measure: number of songs AI-suggested to users
Dimension: Song (dimension attribute: rankCategory, currentFlag)

How many users’ favorite song is on the charts and was suggested to them?
Measure: number of AI-suggested songs marked as favorite
Dimension: Song (dimension attribute: rankCategory, currentFlag)
Degenerate Dimension: AI_Suggested
 
Songs from which genre are most commonly AI-suggested to users?
Measure: number of songs AI-suggested to users
Dimension: Song (dimension attributes: genre) 
Checking if there is Data in the Data sources needed to fill the Data warehouse


TABLE NAME
COLUMN
SOURCE
SONGLOG
One tuple describe one fact of a user listening to a song


SongID
Song ID. Foreign key from dimension table sourced in reference FK_Song in Song table of the RDB 


UserID
User Id. Foreign key from dimension table sourced in reference FK_User in User table of the RDB 


id_StartTimeStamp 
Time stamp of when a user started playing a specific song sourced from column startTimesStamp of Songlog table of the RDB 


id_EndTimeStamp
Time stamp of when a user stopped playing a specific song  sourced from column endTimesStamp of Songlog table of the RDB 


AI_suggested
Method used by a user to find and play a song  sourced from column MethodOfSelection of Songlog table  of the RDB 
Allowed Values:
Search = 0
AI-Suggestion = 1


id_date_startTime
Date stamp of when a user started playing a specific song sourced from column DateStartTime of  Songlog table of the RDB 


id_date_endTime
Date stamp of when a user stopped playing a specific song  sourced from column DateEndTime of  Songlog table of the RDB 


Listening_Percentage
Percentage recorded in the case when a song was stopped before end 0-100 sourced from Songlog table of the RDB 


Time_Listened_To
Time Listened to a specific song in minutes. Calculated from the percentage of listening and Duration Of Song column from Song Table in RDB


isFavoriteSong
Marks if a song is a users favorie song obtained from id_Favorite_song from User table in  the RDB and Song ID from Song table in RDB


isFavoriteAndAIsuggestedSong
IF song is favorite and was AI-suggested to user = 1, else = 0  sourced from column isFavoriteAndAIsuggestedSong of  Songlog table of the RDB


StoppedListening
If song was stopped during listening = 1, else = 0  sourced from column StoppedListening of  Songlog table of the RDB


DurationOfSong
Duration of song in Minutes sourced from column DurationOfSong of  Songlog table of the RDB
TIMELOG
One Tuple describes one session of a user From logging on to logging off)


ID
Log ID. Surrogate key - generated by database


UserID
User Id. Foreign key from User dimension table of the RDB


id_date_SessionStart
Day, month and year of when a user started using the Spotify app sourced from column dateSessionStart of TimeLog table of the RDB 


id_date_SessionEnd
Day, month and year of when a user stopped using the Spotify app sourced from column dateSessionEnd of   TimeLog table of the RDB 


TimeSpent
Amount of time spent by a user on the app in minutes sourced from column TimesSpent of  TimeLog Table of the RDB


id_time_SessionStart
Hour, minute, second of when a user started using the Spotify app sourced from column timeSessionStart of  TimeLog table of the RDB 


id_time_SessionEnd
Hour, minute, second of when a user stopped using the Spotify app sourced from column timeSessionEnd of  TimeLog table of the RDB 


Num_of_songs
Number of songs listened to in a session sourced from column Num_of_songs of  TimeLog table of the RDB 


Num_of_artists
Number of artists listened to in a session sourced from column Num_of_artists of  TimeLog table of the RDB 


TimeSpent_Choosing_Songs
Time in minutes spent choosing songs in a session sourced from column TimeSpent_Choosing_Songs of  TimeLog table of the RDB 
SONG
One tuple describes one song 


SongID
Song ID. Surrogate key - generated by database


SongName
Name of song name sourced fromcolumn SongName of  Song table of the RDB


id_PublicationDate
Date of Song publication sourced from column PublicationDate of  Song table of the RDB


Artist
Name of the artist sourced from column Artist of  the Song table of the RDB


Writer
Name of the writer sourced from column Writer of the Song table of the RDB


Producer
Name of the producer sourced column Producer of from the Song table of the RDB


Genre
Name of the songgenre sourced from column Genre of the Song table of the RDB Allowed values are listed on this website: Music Genre List - A complete list of music styles, types and genres (musicgenreslist.com) 


Streams
Number of streams of one song taken from column Streams of Song table of the RDB. Allowed values: LITTLE for 0 - 1000, NORMAL for 1001- 5000, A LOT for 5001+


rankCategory
The ranking position on the song charts taken from charts CSV file. Allowed values: from 1 to 20, from 21 to 50, from 51  to  70, from 71 to 100


currentFlag
Flag indicating the current version. Allowed values Y for current, N for old data. Sourced from column currentFlag of Song table of the RDB. 


LabelID
Label Id. Foreign key from dimension table Label took from Label table of the RDB
USER
One tuple describes one user


UserID
User ID. Surrogate key - generated by database sourced from column UserID of User Table of the RDB


Username
Users username stored in User table (Varchar) sourced from column username of User Table of the RDB


Email
Users email address taken from column email of User table (Varchar) of the RDB


SongID
The ID of users favourite song stored in User table. Value based on the SongID from Song table of the RDB.
LABEL
One tuple describes one label


LabelID
Label ID. Surrogate key - generated by database sourced from column labelID of  Label Table of the RDB


LabelName
Name of the label taken from column LabelName of  Label table of the RDB.


MainGenre
Name of the genre that one label produces taken from column MainGenre of   Label table of the RDB Allowed values are listed on this website: Music Genre List - A complete list of music styles, types and genres (musicgenreslist.com) 


NumberOfSongs
Number of songs that label were taken from column NumberOfSongs of  Label table (Integer) of the RDB. Allowed values: LITTLE for 0 - 1000, NORMAL for 1001- 5000, A LOT for 5001+


Id_CreationDate
Creation date of the label DD-MM-YYYY taken from column LabelCreationDate of  Label table of the RDB


Id_ContractStart
The date of starting a contract with label DD-MM-YYYY taken from column ContractStart of Label table of the RDB


Id_ContractEnd
The date of ending a contract with label  DD-MM-YYYY taken from column ContractEnd of  Label table of the RDB


NumberOfArtists
The number of artist who work for the label taken from column NumberOfArtists of Label table of the RDB. Allowed Values:
 LITTLE for 0 - 100, NORMAL for 101- 500, A LOT for 501+


CostOfContract
The cost of making a contract with label taken from column CostOfContract of  Label table of the RDB. Allowed values: CHEAP for 0-10000, NORMAL for 10000-50000 and EXPENSIVE for 50000+
Date
One tuple describes one day.



All the data in this table are generated tuple by tuple based on any
calendar, before ETL process
Time
One tuple describes one second (independently of date).
 All the data in this table are generated tuple by tuple based on clock, before ETL process





