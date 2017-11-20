

# Wrangling WeRateDogs Twitter Data

For this project I wrangled data on the Twitter profile WeRateDogs in three steps: gather, assess, and clean. 

## Gather
Three sources of data were gathered: 

1. `twitter-archive-enhanced.csv`, was provided by Udacity  and contained data that their staff had previously scraped from WeRateDogs twitter posts. Its main features about the tweet itself, not its performance. This included urls of original posts if the observation was a retweet or a reply and extracted information from the post text such as the stage of development and dog rating.

2. `image-predictions.tsv`, was also provided by Udacity and programmatically downloaded from the target url provided. It contained a table of breed for each post's photo as predicted by a convolutional neural net. 

3. `tweet_json.txt`, was collected from the Twitter API, using the tweet ids provided in  `twitter-archive-enhanced.csv`. Each tweets full JSON data was written to the file, but only tweet performance (retweets and favorites) and descriptive features about the tweets (time, media_type) were extracted. 

Each table was then read into a pandas data frame:  `dogs`, `breeds`, and `tweet_scraped`

## Assess

I assessed the data through a combination of programatic and manual exploration. It was immediately obvious that data on different sheets were of the same observational unit and should be combined. Within the `dogs` table, some tweets were retweets, which should not be in the data set.[^instructions]  Stage columns (doggo, pupper, puppo, and floofer) at first seemed categorical, but because some posts had more that one type of dog condensing them into one column would have ultimately made the data more difficult to work with or lost data (not mutually exclusive). I decided to keep them in four columns, but convert them to boolean values. It was also noted that there was a warning in the instructions that these stages may not have been properly extracted.  A small number of  scores had very high numerator values (e.g. 1776, 420, 666), which upon exploration were found to be puns. 

The dog `breeds` table had fewer values than the main `dogs` table, which could be explained by some tweets not having photos. Many of the predictions had low confidence (<0.5). 

The `tweet_scraped` data was missing several (5) values due to tweets being deleted. The `media_type` columns highlighted that some tweets did not have photos, which is an inclusion criteria.[^instructions] 

All tables has columns of the incorrect type. 

## Clean

Each cleaning operation was defined, executed, and then tested. Copies were made of each data frame and  `breeds_clean` and `tweet_scraped_clean` were left-merged into `dogs_clean`. Only two columns were merged from `breeds` - predicted breed, and a boolean array if that breed is a type of dog. Both were merged only for values that had confidence greater than 0.5. Observations that were retweets or that did not have photos were then dropped as were features that pertained only to these observations. Stage columns (doggo, pupper, puppo, floofer), were recalculated using regular expressions to search the text of each tweet with a boolean value assigned to each column. Features that were not descriptive (source url,  url of original tweet if the row was a reply, etc.) were moved to a separate table, `tweet_info` and a boolean column `is_reply` was added to `dogs_clean` to separate observational types between tables. Finally columns were converted to the correct type if so needed and column names that were undescriptive were renamed to be more understandable. `dogs_clean` was then exported to `twitter_archive_master.csv`.


[^instructions]: According to instructions in the Udacity Project Assignment
