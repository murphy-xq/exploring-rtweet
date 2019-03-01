Exploring rtweet
================

## Machine learning flashcards

Looking for a way to reinforce machine learning concepts, I happened
upon the Chris Albon’s [Machine Learning
Flashcards](https://machinelearningflashcards.com/) on Twitter. And,
after reading the accompanying website, I noticed that Chris links to [a
Python repo](https://github.com/Dpananos/GetCards) that scrapes the
images from Chris’s Twitter feed. I thought I would try to do the same
using R so here we go:

### Pull tweets and inspect the output

First, we have a go at pulling tweets from Chris’s feed via
`rtweet`:

``` r
albon_tweets <- get_timeline(user = "chrisalbon", n = 3200)  # max return value
head(albon_tweets)
#> # A tibble: 6 x 88
#>   user_id status_id created_at          screen_name text  source display_text_wi… reply_to_status…
#>   <chr>   <chr>     <dttm>              <chr>       <chr> <chr>             <dbl> <chr>           
#> 1 115185… 11012538… 2019-02-28 22:52:29 chrisalbon  "@jG… Twitt…               69 110125104169218…
#> 2 115185… 11012529… 2019-02-28 22:48:55 chrisalbon  @dpa… Twitt…               54 <NA>            
#> 3 115185… 11012409… 2019-02-28 22:01:14 chrisalbon  As a… Twitt…               74 <NA>            
#> 4 115185… 11011904… 2019-02-28 18:40:30 chrisalbon  Vari… Machi…               32 <NA>            
#> 5 115185… 11008252… 2019-02-27 18:29:18 chrisalbon  Deri… Machi…               34 <NA>            
#> 6 115185… 11004606… 2019-02-26 18:20:41 chrisalbon  Why … Machi…               56 <NA>            
#> # … with 80 more variables: reply_to_user_id <chr>, reply_to_screen_name <chr>, is_quote <lgl>,
#> #   is_retweet <lgl>, favorite_count <int>, retweet_count <int>, hashtags <list>, symbols <list>,
#> #   urls_url <list>, urls_t.co <list>, urls_expanded_url <list>, media_url <list>, media_t.co <list>,
#> #   media_expanded_url <list>, media_type <list>, ext_media_url <list>, ext_media_t.co <list>,
#> #   ext_media_expanded_url <list>, ext_media_type <chr>, mentions_user_id <list>,
#> #   mentions_screen_name <list>, lang <chr>, quoted_status_id <chr>, quoted_text <chr>,
#> #   quoted_created_at <dttm>, quoted_source <chr>, quoted_favorite_count <int>, quoted_retweet_count <int>,
#> #   quoted_user_id <chr>, quoted_screen_name <chr>, quoted_name <chr>, quoted_followers_count <int>,
#> #   quoted_friends_count <int>, quoted_statuses_count <int>, quoted_location <chr>, quoted_description <chr>,
#> #   quoted_verified <lgl>, retweet_status_id <chr>, retweet_text <chr>, retweet_created_at <dttm>,
#> #   retweet_source <chr>, retweet_favorite_count <int>, retweet_retweet_count <int>, retweet_user_id <chr>,
#> #   retweet_screen_name <chr>, retweet_name <chr>, retweet_followers_count <int>,
#> #   retweet_friends_count <int>, retweet_statuses_count <int>, retweet_location <chr>,
#> #   retweet_description <chr>, retweet_verified <lgl>, place_url <chr>, place_name <chr>,
#> #   place_full_name <chr>, place_type <chr>, country <chr>, country_code <chr>, geo_coords <list>,
#> #   coords_coords <list>, bbox_coords <list>, status_url <chr>, name <chr>, location <chr>,
#> #   description <chr>, url <chr>, protected <lgl>, followers_count <int>, friends_count <int>,
#> #   listed_count <int>, statuses_count <int>, favourites_count <int>, account_created_at <dttm>,
#> #   verified <lgl>, profile_url <chr>, profile_expanded_url <chr>, account_lang <chr>,
#> #   profile_banner_url <chr>, profile_background_url <chr>, profile_image_url <chr>
```

Okay, after reading Twitter’s standard search API
[documentation](https://developer.twitter.com/en/docs/tweets/search/overview/standard)
we see that the standard search API will only return a sampling of the
user’s Tweets published in the past **7 days**. So, we will be
retrieving just a sample of Chris’s handy flashcards.

Also, there were nearly 90 variables returned so let’s take a `glimpse`
and zero in on what is essential for this specific task:

``` r
glimpse(albon_tweets)
#> Observations: 436
#> Variables: 88
#> $ user_id                 <chr> "11518572", "11518572", "11518572", "11518572", "11518572", "11518572", "11…
#> $ status_id               <chr> "1101253837317382144", "1101252937769238528", "1101240938813370368", "11011…
#> $ created_at              <dttm> 2019-02-28 22:52:29, 2019-02-28 22:48:55, 2019-02-28 22:01:14, 2019-02-28 …
#> $ screen_name             <chr> "chrisalbon", "chrisalbon", "chrisalbon", "chrisalbon", "chrisalbon", "chri…
#> $ text                    <chr> "@jGage718 @cercerilla @statwonk @digitalocean That doesnt even sound like …
#> $ source                  <chr> "Twitter for iPhone", "Twitter for iPhone", "Twitter for iPhone", "Machine …
#> $ display_text_width      <dbl> 69, 54, 74, 32, 34, 56, 121, 74, 126, 75, 67, 4, 65, 3, 25, 48, 81, 194, 88…
#> $ reply_to_status_id      <chr> "1101251041692184576", NA, NA, NA, NA, NA, NA, "1100119723310804994", NA, "…
#> $ reply_to_user_id        <chr> "637197054", "14839109", NA, NA, NA, NA, NA, "110474012", NA, "22803302", "…
#> $ reply_to_screen_name    <chr> "jGage718", "dpatil", NA, NA, NA, NA, NA, "MattGallagher0", NA, "ArmsContro…
#> $ is_quote                <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, …
#> $ is_retweet              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, FALSE,…
#> $ favorite_count          <int> 5, 4, 92, 15, 23, 20, 0, 0, 125, 0, 4, 1, 0, 1, 1, 2, 2, 55, 10, 0, 0, 11, …
#> $ retweet_count           <int> 0, 0, 8, 0, 5, 1, 120, 0, 11, 0, 0, 0, 76, 0, 0, 0, 0, 4, 1, 168, 3, 3, 10,…
#> $ hashtags                <list> [NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
#> $ symbols                 <list> [NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
#> $ urls_url                <list> ["jobs.lever.co/devoted/", "twitter.com/jgage718/statu…", "twitter.com/jet…
#> $ urls_t.co               <list> ["https://t.co/cKjUKd8pvo", "https://t.co/tKyVxVG1sf", "https://t.co/Plg5V…
#> $ urls_expanded_url       <list> ["https://jobs.lever.co/devoted/", "https://twitter.com/jgage718/status/11…
#> $ media_url               <list> [NA, NA, NA, "http://pbs.twimg.com/media/D0g22vCVYAASrTY.png", "http://pbs…
#> $ media_t.co              <list> [NA, NA, NA, "https://t.co/vUoLldeqiA", "https://t.co/kweIzXBfjR", "https:…
#> $ media_expanded_url      <list> [NA, NA, NA, "https://twitter.com/chrisalbon/status/1101190424276819968/ph…
#> $ media_type              <list> [NA, NA, NA, "photo", "photo", "photo", NA, NA, NA, NA, NA, NA, "photo", N…
#> $ ext_media_url           <list> [NA, NA, NA, "http://pbs.twimg.com/media/D0g22vCVYAASrTY.png", "http://pbs…
#> $ ext_media_t.co          <list> [NA, NA, NA, "https://t.co/vUoLldeqiA", "https://t.co/kweIzXBfjR", "https:…
#> $ ext_media_expanded_url  <list> [NA, NA, NA, "https://twitter.com/chrisalbon/status/1101190424276819968/ph…
#> $ ext_media_type          <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
#> $ mentions_user_id        <list> [<"637197054", "2777112211", "944231", "457033547">, "14839109", NA, NA, N…
#> $ mentions_screen_name    <list> [<"jGage718", "cercerilla", "statwonk", "digitalocean">, "dpatil", NA, NA,…
#> $ lang                    <chr> "en", "en", "en", "en", "en", "en", "en", "en", "en", "en", "en", "fr", "en…
#> $ quoted_status_id        <chr> NA, "1101251379660836870", "1101200154961469440", NA, NA, NA, NA, NA, NA, N…
#> $ quoted_text             <chr> NA, "What they didn’t tell you about the data team is that you get all the …
#> $ quoted_created_at       <dttm> NA, 2019-02-28 22:42:43, 2019-02-28 19:19:10, NA, NA, NA, NA, NA, NA, NA, …
#> $ quoted_source           <chr> NA, "Twitter for iPhone", "TweetDeck", NA, NA, NA, NA, NA, NA, NA, NA, NA, …
#> $ quoted_favorite_count   <int> NA, 15, 1138, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 2571,…
#> $ quoted_retweet_count    <int> NA, 1, 571, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 770, NA…
#> $ quoted_user_id          <chr> NA, "637197054", "18955413", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
#> $ quoted_screen_name      <chr> NA, "jGage718", "jetjocko", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
#> $ quoted_name             <chr> NA, "Justin Gage", "Adam Rogers", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
#> $ quoted_followers_count  <int> NA, 2231, 14911, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 33…
#> $ quoted_friends_count    <int> NA, 910, 1020, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 494,…
#> $ quoted_statuses_count   <int> NA, 17459, 26848, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 1…
#> $ quoted_location         <chr> NA, "New York, NY", "California", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
#> $ quoted_description      <chr> NA, "Product Data @digitalocean for that bread, freelance data content for …
#> $ quoted_verified         <lgl> NA, FALSE, TRUE, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, TR…
#> $ retweet_status_id       <chr> NA, NA, NA, NA, NA, NA, "1100432336905068544", NA, NA, NA, NA, NA, "1099900…
#> $ retweet_text            <chr> NA, NA, NA, NA, NA, NA, "applying for a data science job in 2010 vs. applyi…
#> $ retweet_created_at      <dttm> NA, NA, NA, NA, NA, NA, 2019-02-26 16:28:08, NA, NA, NA, NA, NA, 2019-02-2…
#> $ retweet_source          <chr> NA, NA, NA, NA, NA, NA, "Twitter Web App", NA, NA, NA, NA, NA, "Twitter for…
#> $ retweet_favorite_count  <int> NA, NA, NA, NA, NA, NA, 549, NA, NA, NA, NA, NA, 390, NA, NA, NA, NA, NA, N…
#> $ retweet_retweet_count   <int> NA, NA, NA, NA, NA, NA, 120, NA, NA, NA, NA, NA, 76, NA, NA, NA, NA, NA, NA…
#> $ retweet_user_id         <chr> NA, NA, NA, NA, NA, NA, "47436444", NA, NA, NA, NA, NA, "1558406653", NA, N…
#> $ retweet_screen_name     <chr> NA, NA, NA, NA, NA, NA, "jrmontag", NA, NA, NA, NA, NA, "EpiEllie", NA, NA,…
#> $ retweet_name            <chr> NA, NA, NA, NA, NA, NA, "Josh Montague \U0001f4ca\U0001f389", NA, NA, NA, N…
#> $ retweet_followers_count <int> NA, NA, NA, NA, NA, NA, 2946, NA, NA, NA, NA, NA, 4717, NA, NA, NA, NA, NA,…
#> $ retweet_friends_count   <int> NA, NA, NA, NA, NA, NA, 2619, NA, NA, NA, NA, NA, 1172, NA, NA, NA, NA, NA,…
#> $ retweet_statuses_count  <int> NA, NA, NA, NA, NA, NA, 40331, NA, NA, NA, NA, NA, 12631, NA, NA, NA, NA, N…
#> $ retweet_location        <chr> NA, NA, NA, NA, NA, NA, "Golden + Boulder (CO)", NA, NA, NA, NA, NA, "", NA…
#> $ retweet_description     <chr> NA, NA, NA, NA, NA, NA, "counts \U0001f425\U0001f4ac, drinks \u2615️, takes…
#> $ retweet_verified        <lgl> NA, NA, NA, NA, NA, NA, FALSE, NA, NA, NA, NA, NA, FALSE, NA, NA, NA, NA, N…
#> $ place_url               <chr> "https://api.twitter.com/1.1/geo/id/67b98f17fdcf20be.json", "https://api.tw…
#> $ place_name              <chr> "Boston", "Boston", "Boston", NA, NA, NA, NA, "California", "California", "…
#> $ place_full_name         <chr> "Boston, MA", "Boston, MA", "Boston, MA", NA, NA, NA, NA, "California, USA"…
#> $ place_type              <chr> "city", "city", "city", NA, NA, NA, NA, "admin", "admin", "admin", "admin",…
#> $ country                 <chr> "United States", "United States", "United States", NA, NA, NA, NA, "United …
#> $ country_code            <chr> "US", "US", "US", NA, NA, NA, NA, "US", "US", "US", "US", "US", NA, "US", "…
#> $ geo_coords              <list> [<NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA…
#> $ coords_coords           <list> [<NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA, NA>, <NA…
#> $ bbox_coords             <list> [<-71.19142, -70.98600, -70.98600, -71.19142, 42.22780, 42.22780, 42.39954…
#> $ status_url              <chr> "https://twitter.com/chrisalbon/status/1101253837317382144", "https://twitt…
#> $ name                    <chr> "Chris Albon", "Chris Albon", "Chris Albon", "Chris Albon", "Chris Albon", …
#> $ location                <chr> "San Francisco", "San Francisco", "San Francisco", "San Francisco", "San Fr…
#> $ description             <chr> "Using data to fight for something that matters. Data science @DevotedHealt…
#> $ url                     <chr> "https://t.co/CQhzAA24cn", "https://t.co/CQhzAA24cn", "https://t.co/CQhzAA2…
#> $ protected               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE…
#> $ followers_count         <int> 31818, 31818, 31818, 31818, 31818, 31818, 31818, 31818, 31818, 31818, 31818…
#> $ friends_count           <int> 705, 705, 705, 705, 705, 705, 705, 705, 705, 705, 705, 705, 705, 705, 705, …
#> $ listed_count            <int> 1345, 1345, 1345, 1345, 1345, 1345, 1345, 1345, 1345, 1345, 1345, 1345, 134…
#> $ statuses_count          <int> 485, 485, 485, 485, 485, 485, 485, 485, 485, 485, 485, 485, 485, 485, 485, …
#> $ favourites_count        <int> 10316, 10316, 10316, 10316, 10316, 10316, 10316, 10316, 10316, 10316, 10316…
#> $ account_created_at      <dttm> 2007-12-26 01:49:09, 2007-12-26 01:49:09, 2007-12-26 01:49:09, 2007-12-26 …
#> $ verified                <lgl> TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRU…
#> $ profile_url             <chr> "https://t.co/CQhzAA24cn", "https://t.co/CQhzAA24cn", "https://t.co/CQhzAA2…
#> $ profile_expanded_url    <chr> "http://ChrisAlbon.com", "http://ChrisAlbon.com", "http://ChrisAlbon.com", …
#> $ account_lang            <chr> "en", "en", "en", "en", "en", "en", "en", "en", "en", "en", "en", "en", "en…
#> $ profile_banner_url      <chr> "https://pbs.twimg.com/profile_banners/11518572/1503261622", "https://pbs.t…
#> $ profile_background_url  <chr> "http://abs.twimg.com/images/themes/theme1/bg.png", "http://abs.twimg.com/i…
#> $ profile_image_url       <chr> "http://pbs.twimg.com/profile_images/736992518110224384/fmqQxFEr_normal.jpg…
```

Right off the bat I see `machinelearningflashcards.com` under the
`urls_url` column which will helpful in filtering the data.
Additionally, `media_url` conveniently contains the flashcard image url.

I will be sure to grab those variables in addition to the Twitter handle
and accompany text for reference. Using a series of `stringr` functions,
I strip out the name of the flashcard from `text` to be used later on.

Lastly, having a glance at the `flashcard_name` column shows that some
flashcards are repeated. So, let’s use `distinct` to keep only the
unique records, noting the convenient `.keep_all = TRUE` argument to
retain all columns of the dataframe.

``` r
flash_df <- albon_tweets %>% 
  select(screen_name, text, urls_url, media_url) %>% 
  unnest(urls_url, .preserve = media_url) %>%
  filter(str_detect(urls_url, "machinelearning"),    # keep tweets containing the flashcard url
         !is.na(media_url)) %>%                      # drop any tweets lacking an image
  unnest(media_url) %>% 
  mutate(
    flashcard_name = text %>% 
      str_extract(".+?(?=\\shttps)") %>% 
      str_to_lower() %>% 
      str_replace_all("\\s", "-")
    ) %>% 
  distinct(flashcard_name, .keep_all = TRUE)

flash_df
#> # A tibble: 22 x 5
#>    screen_name text                              urls_url         media_url               flashcard_name      
#>    <chr>       <chr>                             <chr>            <chr>                   <chr>               
#>  1 chrisalbon  Variance https://t.co/eZ2bbpDzwV… machinelearning… http://pbs.twimg.com/m… variance            
#>  2 chrisalbon  Derivative https://t.co/eZ2bbpDz… machinelearning… http://pbs.twimg.com/m… derivative          
#>  3 chrisalbon  Why Is It Called A Cost Function… machinelearning… http://pbs.twimg.com/m… why-is-it-called-a-…
#>  4 chrisalbon  Meanshift Clustering By Analogy … machinelearning… http://pbs.twimg.com/m… meanshift-clusterin…
#>  5 chrisalbon  Interquartile Range https://t.co… machinelearning… http://pbs.twimg.com/m… interquartile-range 
#>  6 chrisalbon  Training And Test Error https://… machinelearning… http://pbs.twimg.com/m… training-and-test-e…
#>  7 chrisalbon  Saddle Point https://t.co/eZ2bbp… machinelearning… http://pbs.twimg.com/m… saddle-point        
#>  8 chrisalbon  Frobenius Norm https://t.co/eZ2b… machinelearning… http://pbs.twimg.com/m… frobenius-norm      
#>  9 chrisalbon  Hidden Layer https://t.co/eZ2bbp… machinelearning… http://pbs.twimg.com/m… hidden-layer        
#> 10 chrisalbon  Matthews Correlation Coefficient… machinelearning… http://pbs.twimg.com/m… matthews-correlatio…
#> # … with 12 more rows
```

### Read and write the flashcards

Now that we have the `flash_df` dataframe containing the image URLs and
names, let’s make a quick function to read and write the flashcard
images using `magick` and then feed those parameters into `pwalk` to
iterate through the flashcards that we identified above:

``` r
grab_flash <- function(flash_url, flash_name, folder) {
  flash_url %>% 
    image_read() %>% 
    image_write(here(folder, str_c(flash_name,".png")))
}

params <- list(pull(flash_df, media_url), pull(flash_df, flashcard_name)) 
pwalk(params, ~grab_flash(.x, .y, "ml-flashcard-images"))
```

## You draw an X how?\!?\!

Trolling through [\#rstats](https://twitter.com/hashtag/rstats) Twitter,
I came across this tweet soliciting responses on how people draw an X:

<img src="how-to-draw-x_image.png" width="40%" />

Interestingly, @SMASEY observed that: “General consensus is that
Americans do 7 & 8 while UK does 5 & 6. Probably how we were taught.” Is
that the case? Let’s see what the data say.

### Pull responses and wrangle a tidy dataframe

The standard search API only returns data from the previous 7 days which
presents a problem as this tweet is from January 20, 2019. There is,
however, a way forward using the [30-Day
API](https://developer.twitter.com/en/docs/tweets/search/quick-start/premium-30-day)
which requires one to register a developer account, a registered app,
and a developer environment setup. [See here for a walkthrough of that
process](https://rud.is/books/21-recipes/using-oauth-to-access-twitter-apis.html).
Note that while we can still search for free, we are now constrained to
100 tweets per request with a cap of 250 requests per month.

Digging into Twitter’s historical data means that we will have to leave
the simplicity of `rtweet` behind and roll our own function to pull data
from the [30-Day
API](https://developer.twitter.com/en/docs/tweets/search/quick-start/premium-30-day).

``` r
pull_tweets <- function(url,                 # 30-day search stem
                        dev_env,             # <YOUR_DEV_ENV_NAME>
                        tkn,                 # <YOUR_BEARER_TOKEN> (see rtweet::bearer_token)
                        search = NULL,       # search terms   
                        start_date = NULL,   # <YYYYMMDDHHmm>
                        stop_date = NULL,    # <YYYYMMDDHHmm>
                        max_req = NULL       # integer to limit requests (250/month cap)
                        ) {
  # construct url
  thirty_url <- str_c(url, dev_env, ".json")
  
  # inital call
  res <- GET(thirty_url,
             query = list(query = search, fromDate = start_date, toDate = stop_date),
             add_headers(Authorization = tkn))
  
  out <- fromJSON(read_lines(res[["content"]]), flatten = TRUE) %>% .[['results']]
  nxt_tkn <- fromJSON(read_lines(res[["content"]])) %>% .[['next']]
  output_init <- list(list(df = out, nxt = nxt_tkn))
  
  print("call_1")
  
  # loop until max_req limit or `next` token unavailable
  i <- 1
  output_loop <- list()
  
  while (!is.null(nxt_tkn) && i <= max_req - 1) {
    res <- GET(thirty_url,
               query = list(query = search, fromDate = start_date, toDate = stop_date, `next` = nxt_tkn),
               add_headers(Authorization = tkn))
    
    out <- fromJSON(read_lines(res[["content"]]), flatten = TRUE) %>% .[['results']]
    nxt_tkn <- fromJSON(read_lines(res[["content"]])) %>% .[['next']]
    output_loop[[i]] <- list(df = out, nxt = nxt_tkn)
    
    i <- i + 1
    print(str_c("call_", i))
  }
  append(output_init, output_loop)
}
```

Let’s use `pull_tweets` to grab all responding tweets from the US and
the UK. Boilerplate code is provided below should you wish to try using
your own credentials.

``` r
pull_tweets(url = "https://api.twitter.com/1.1/tweets/search/30day/",
            dev_env = <YOUR_DEV_ENV_NAME>,
            tkn = <YOUR_BEARER_TOKEN>,
            search = "to:SMASEY place_country:US",  # `place_country:GB` for UK tweets
            max_req = 10)                           # set a limit on number of requests
```

Here we read in the data collected using `pull_tweets`; wrangle the
output into a tidy dataframe; and, use some coarse `regex` to extract
the answer which should be an integer between 1 and 8.

``` r
tweets_df <- fs::dir_ls(here("how-to-draw-x_data"), glob = "*.rds") %>% 
  map(read_rds) %>%       # read in output files
  map_depth(2, "df") %>%  # grab df inside each nested list
  flatten_dfr() %>%       # flatten list and row bind
  filter(str_detect(text, "@SMASEY\\shttps+", negate = TRUE)) %>% 
  mutate(ans = text %>% 
           str_extract("\\s\\d{1}(?!\\d)") %>% 
           str_squish()) %>% 
  na.omit()
```

### Time to plot the data

In total, it looks like we grabbed 434 tweets: 260 from the US and 174
from the UK. Let’s take a quick look at the distribution of answer
choices by country.

``` r
# histograms
ggplot(tweets_df, aes(x = as.numeric(ans), fill = country_code)) + 
  geom_histogram(binwidth = 1, color = "white") + 
  geom_text(stat = 'count', aes(label = ..count.., vjust = -0.2)) + 
  scale_x_continuous(breaks = seq(1, 8, 1)) + 
  coord_cartesian(clip = "off") + 
  facet_wrap(~country_code, nrow = 2) + 
  theme_minimal(base_size = 10) + 
  labs(x = "answer choice", y = "count") + 
  guides(fill = FALSE, color = FALSE)
```

<img src="exploring-rtweet_files/figure-gfm/unnamed-chunk-9-1.png" width="75%" style="display: block; margin: auto;" />

### Is location associated with how one draws an X?

Okay, it is clear that 7 & 8 are the most popular answers in both
countries followed by 5 & 6. Answer choices 1-4 are infrequent (fewer
than ~4% of responses in both cases). However, it is not clear what, if
any, association exists between country and answer choice.

Let’s use the `infer` package to run a simulation-based test to
investigate whether there is an association between location and answer
choice. The `infer` package’s intuitive design makes it straightforward
for us to: (1) calculate our chi-squared statistic; (2) simulate a null
distribution through permutation; and, (3) calculate the proportion of
replicates that had a chi-squared as or more extreme than the observed
statistic to determine significance.

``` r
# for reproducibility
set.seed(2)

# set up df
chisq_df <- tweets_df %>% 
  mutate(ans_fct = fct_lump(ans, prop = 0.05), # use fct_lump() for infrequent factor levels
         country_code = factor(country_code))

# calculate test statistic
obs_chisq <- chisq_stat(chisq_df, ans_fct ~ country_code)
obs_chisq
#> # A tibble: 1 x 1
#>    stat
#>   <dbl>
#> 1  24.4

# generate null distribution
null_dist_chisq <- chisq_df %>% 
  specify(ans_fct ~ country_code) %>% 
  hypothesize(null = "independence") %>%
  generate(reps = 3000, type = "permute") %>%
  calculate(stat = "Chisq")

# inspect test statistic within null distribution
visualize(null_dist_chisq) + 
  shade_p_value(obs_stat = obs_chisq, direction = "greater")
```

<img src="exploring-rtweet_files/figure-gfm/unnamed-chunk-10-1.png" width="75%" style="display: block; margin: auto;" />

``` r
# grab p-value
pval_chisq <- get_p_value(null_dist_chisq, obs_stat = obs_chisq, direction = "greater")
pval_chisq
#> # A tibble: 1 x 1
#>    p_value
#>      <dbl>
#> 1 0.000333
```

After generating a simulation-based null distribution of chi-squared
statistics, we see that there is a 0.03% chance of observing a
chi-squared value at least as large as 24.4 in a world where there’s no
difference between `country` and `ans`. So, we observe strong evidence
in support of a significant association between location and how one
draws an X.

### What about \# 7 & 8 being particularly American?

Well, we’ve see that there is some association with how an X is drawn
and location, so let’s be a bit more specific and tease apart this
observation that 7 & 8 might be particularly American. To do this we
will set up a dataframe with a collapsed factor variable for answers 7 &
8, take a peek at the proportions of answers 7 & 8 by location, and then
test any observed difference in proportions across countries.

``` r
# set up df
props_df <- tweets_df %>% 
  mutate(ans_clps = fct_collapse(ans,
                                 one_six = c("1","2","3","4","5","6"),
                                 svn_egt = c("7","8")),
         country_code = factor(country_code))

# calculate counts and proportions
props_df %>% 
  tabyl(country_code, ans_clps) %>% 
  adorn_percentages("row") %>% 
  adorn_pct_formatting(digits = 1) %>%
  adorn_ns()
#>  country_code    one_six     svn_egt
#>            GB 33.9% (59) 66.1% (115)
#>            US 17.3% (45) 82.7% (215)
```

We observe a difference of ~17 percentage points for answers \# 7 & 8
between US and UK responses. Let’s employ the same `infer` workflow from
before to implement a simulation-based test on this observed difference
in proportions.

``` r
# for reproducibility
set.seed(20)

# calculate test statistic
d_hat <- props_df %>% 
  specify(ans_clps ~ country_code, success = "svn_egt") %>%
  calculate(stat = "diff in props", order = c("US", "GB"))

# generate null distribution
null_dist_props <- props_df %>%
  specify(ans_clps ~ country_code, success = "svn_egt") %>%
  hypothesize(null = "independence") %>% 
  generate(reps = 3000, type = "permute") %>% 
  calculate(stat = "diff in props", order = c("US", "GB"))

# inspect test statistic within null distribution
visualize(null_dist_props) +
  shade_p_value(obs_stat = d_hat, direction = "two_sided")
```

<img src="exploring-rtweet_files/figure-gfm/unnamed-chunk-13-1.png" width="75%" style="display: block; margin: auto;" />

``` r
# grab p-value
pval_props <- get_p_value(null_dist_props, obs_stat = d_hat, direction = "two_sided")
pval_props
#> # A tibble: 1 x 1
#>    p_value
#>      <dbl>
#> 1 0.000667
```

We see that there is a 0.07% chance of a test statistic at least as
extreme as ±0.166 in a world where there is no difference in proportions
by location. So, we observe strong evidence in support of a significant
difference in responses of 7 & 8 by location, with a higher proportion
of 7 & 8’s from the US.
