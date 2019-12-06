# tweet_classifier
<p>The files in this repository represent coding that was done with the aim of building a classifier for detecting offensive tweets.  If this repository is cloned then all of the files should run "as is" with the exception of those files that have "COLAB" in the title.  The "COLAB" notebooks were written on the Google Colab platform to take advantage of the additional RAM and processing power being offered by Google.  If the COLAB files are to be run then the directories in the code for reading and writing files will need to be updated by the user.  An explanation of each of the files is provided below.</p></br>

<h3>01_DO_NOT_RUN_offensive_tweets</h3>
<p>This code requires Twitter API credentials.  When run, the code queries the twitter api using the tweet ids (McMenemy's annotated tweets) found in 'cps_cat4.txt'.  If the tweet can no longer be found in Twitter then a less rich version of the tweet is obtained from the NVivo export supplied by McMenemy ('csv_excel/full_nvivo_export.xlsx').  The tweet data is then saved in 'pickle_files/existing_hate.pickle'</p></br>

<h3>02_DO_NOT_RUN_hatebase_api</h3>
<p>This code requires Hatebase api credentials.  English hate terms are extracted from the hatebase api and saved in 'pickle_files/hate_terms.pickle'.</p></br>

<h3>03_DO_NOT_RUN_benign_tweets</h3>
<p>This code requires Twitter API credentials.  This code was used on a daily basis to build up an ongoing dataset of tweets using a keyword search.  The full dataset of tweets collected is saved as 'pickle_files/existing_data.pickle'.  The full dataset of tweets was reduced by removing any tweet records where a word from 'pickle_files/hate_terms.pickle' was included.  The reduced dataset was then saved as 'pickle_files/existing_clean.pickle'</p></br>

<h3>04_data_cleansing</h3>
<p>The following files are loaded:</p><ul>
  <li>'pickle_files/existing_data.pickle'</li>
  <li>'pickle_files/existing_clean.pickle'</li>
  <li>'pickle_files/existing_hate.pickle'</li>
  </ul></br>
  <p>The tweet text field is then processed to remove urls, usernames, hashtags, and excess whitespace.  The files are then saved respectively as:</p><ul>
  <li>'pickle_files/api_formatted.pickle'</li>
  <li>'pickle_files/benign_formatted.pickle'</li>
  <li>'pickle_files/offensive_formatted.pickle'</li>
  </ul></br>
  
<h3>05_token_analysis</h3>
  <p>The following files are loaded:</p><ul>
  <li>'pickle_files/benign_formatted.pickle'</li>
  <li>'pickle_files/offensive_formatted.pickle'</li>
  </ul></br>
  <p>An analysis is carried out to find the most frequently occuring terms in each dataset.  Tweets that contain reference to Boris Johnson are removed for reasons detailed in the dissertation from the benign dataset.  The further reduced benign dataset is saved as 'pickle_files/benign_minusbj_formatted.pickle'</p></br>
  
<h3>06_train_eval_split</h3>
<p>'pickle_files/offensive_formatted.pickle' and 'pickle_files/benign_minusbj_formatted.pickle' are merged.  The records are then shuffled and split 80:20 to create 'pickle_files/train_data_formatted.pickle' and 'pickle_files/eval_data_formatted.pickle'</p></br>
  
<h3>07a_tweet_char_ngrams</h3>
<p>The training and evaluation datasets are recombined.  Character 1-4 grams are extracted from each tweet's main text and saved into folders with the following naming convention</p>
<p>features/tweet_char_[n]grams/</p>
<p>the tweets are processed in batches of 500 due to memory issues</p></br>

<h3>07b_tweet_word_ngrams</h3>
<p>The training and evaluation datasets are recombined.  Token 1-4 grams are extracted from each tweet's main text and saved into folders with the following naming convention</p>
<p>features/tweet_word_[n]grams/</p>
<p>the tweets are processed in batches of 500 due to memory issues</p></br>

<h3>07c_bio_char_ngrams</h3>
<p>The training and evaluation datasets are recombined.  Character 1-4 grams are extracted from each tweet's "bio" field and saved into folders with the following naming convention</p>
<p>features/bio_char_[n]grams/</p>
<p>the tweets are processed in batches of 500 due to memory issues</p></br>

<h3>07d_bio_word_ngrams</h3>
<p>The training and evaluation datasets are recombined.  Token 1-4 grams are extracted from each tweet's "bio" field and saved into folders with the following naming convention</p>
<p>features/bio_word_[n]grams/</p>
<p>the tweets are processed in batches of 500 due to memory issues</p></br>

<h3>07d_bio_word_ngrams</h3>
<p>The training and evaluation datasets are recombined.  For each tweet the following features are calculated.  These features are referred to as the "calculated" features in the dissertation</p><ul>
  <li>Count of tokens</li>
  <li>Ave chars per token</li>
  <li>Count of URLs</li>
  <li>Count of Hashtags</li>
  <li>Count of Mentions</li>
  <li>Count of Caps</li>
  <li>Count of Characters</li>
  <li>Count of followers</li>
  <li>Count of Following</li>
  <li>Count of Posted Tweets</li>
  <li>Count of Modals</li>
  <li>Count of tokens with non-alpha characters in the middle (masked)</li>
  <li>Count of 1 character tokens</li>
  <li>Count of quotations</li>
  <li>Count of punctuation</li>
  <li>Sentiment</li>
  </ul>
  <p>A file containing the calculated values for each of these features for each tweet is then saved as 'features/count_features/count_features.pickle'</p></br>
  
  
