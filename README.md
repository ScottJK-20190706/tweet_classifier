# tweet_classifier
<p>The files in this repository represent coding that was done with the aim of building a classifier for detecting offensive tweets.  If this repository is cloned then all of the files should run "as is" with the exception of those files that have "COLAB" in the title.  The "COLAB" notebooks were written on the Google Colab platform to take advantage of the additional RAM and processing power being offered by Google.  If the COLAB files are to be run then the directories in the code for reading and writing files will need to be updated by the user.  An explanation of each of the files is provided below.</p></br>

<h2>Data Gathering and cleansing</h2>
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

<h2>Creating Vector Space Model Bag-of-words features</h2>
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

<h3>07e_counts</h3>
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
  
<h3>08_feature_importance_tfidf</h3>
<p>Many features have been created by this stage.  This file performs a chi-squared test and correlation analysis in order to remove features with little discriminative power.  TF weighting, TFIDF weighting, and frequency count calculations are then made in order to transform the tweets into a range of VSM arrays covering the 3 weighting strategies.  Additional VSM arrays are created where the features from the "bio" field are included also.  Finally BOW VSM arrays with and without the calculated features are created</p></br>

<h2>Classic Vector Space experiments</h2>
<h3>09_classify</h3>
<p>This file performs the "classic vector space" experiments.  The results of the experiments are saved in 'results/metrics_local.pickle' and can be found in appx. 2 of the dissertation.  The 13 files created in the previous experiment are loaded and split into the predefined training and evaluation datasets.  The training and evaluation is then performed using a range of "classic" classifiers"</p></br>

<h2>Classic Embeddings experiments</h2>
<h3>10_doc2vec</h3>
<p>The training and evaluation datasets are merged and the tweet texts are tokenized.  The doc2vec algorithm from the gensim library is then executed over the tweet texts of the merged dataset.  The resulting vectors for each tweet are then used to create a VSM array for both the training and evaluation datasets (two versions of each were created, one with the calculated features and one without).  A range of classic classifiers are then trained and evaluated.  The results are saved under the experiment category of "classic embeddings"</p></br>

<h3>11_COLAB_classify_embeddings_fasttext</h3>
<p>Pre-trained 300d fasttext word embeddings are loaded using the "chakin" module.  The training and evaluation datasets are tokenized and the corresponding embedding is matched to each token in each tweet.  The embeddings are then summed over each tweet to create a single 300d vector per tweet.  A 312d vector is also created for each tweet by joining the calculated features.  The resulting vectors for each tweet are then used to create a VSM array for both the training and evaluation datasets (two versions of each were created, one with the calculated features and one without).  A range of classic classifiers are then trained and evaluated.  The results are saved under the experiment category of "classic embeddings"</p></br>

<h3>12_COLAB_classify_embeddings_glove_twitter</h3>
<p>Pre-trained 200d GloVe word embeddings are loaded using the "chakin" module.  The training and evaluation datasets are tokenized and the corresponding embedding is matched to each token in each tweet.  The embeddings are then summed over each tweet to create a single 200d vector per tweet.  A 212d vector is also created for each tweet by joining the calculated features.  The resulting vectors for each tweet are then used to create a VSM array for both the training and evaluation datasets (two versions of each were created, one with the calculated features and one without).  A range of classic classifiers are then trained and evaluated.  The results are saved under the experiment category of "classic embeddings"</p></br>

<h3>13_COLAB_classify_embeddings_glove</h3>
<p>Pre-trained 300d GloVe word embeddings are loaded using the "chakin" module.  The training and evaluation datasets are tokenized and the corresponding embedding is matched to each token in each tweet.  The embeddings are then summed over each tweet to create a single 300d vector per tweet.  A 312d vector is also created for each tweet by joining the calculated features.  The resulting vectors for each tweet are then used to create a VSM array for both the training and evaluation datasets (two versions of each were created, one with the calculated features and one without).  A range of classic classifiers are then trained and evaluated.  The results are saved under the experiment category of "classic embeddings"</p></br>

<h3>14_COLAB_classify_embeddings_w2v</h3>
<p>Pre-trained 300d word2vec word embeddings are loaded using the "chakin" module.  The training and evaluation datasets are tokenized and the corresponding embedding is matched to each token in each tweet.  The embeddings are then summed over each tweet to create a single 300d vector per tweet.  A 312d vector is also created for each tweet by joining the calculated features.  The resulting vectors for each tweet are then used to create a VSM array for both the training and evaluation datasets (two versions of each were created, one with the calculated features and one without).  A range of classic classifiers are then trained and evaluated.  The results are saved under the experiment category of "classic embeddings"</p></br>

<h2>CNN Embeddings experiments</h2>
<h3>15_COLAB_cnn_fasttext_300</h3>
<p>Pre-trained 300d fasttext word embeddings are loaded using the "chakin" module.  The training and evaluation datasets are tokenized and the corresponding embedding is matched to each token in each tweet.  An array for each tweet is then created by stacking the vectors on top of each other.  Each array for each tweet has dimensions 40 x 300.  If a tweet has less than 40 tokens then the remaining rows of its array are filled with zeros.  A CNN is then trained and evaluated using the corresponding arrays that have been constructed for each instance by this process.  The results of these experiments are saved under the category of "CNN embeddings"</p></br>  

<h3>16_COLAB_cnn_glove300_840</h3>
<p>Pre-trained 300d GloVe word embeddings are loaded using the "chakin" module.  The training and evaluation datasets are tokenized and the corresponding embedding is matched to each token in each tweet.  An array for each tweet is then created by stacking the vectors on top of each other.  Each array for each tweet has dimensions 40 x 300.  If a tweet has less than 40 tokens then the remaining rows of its array are filled with zeros.  A CNN is then trained and evaluated using the corresponding arrays that have been constructed for each instance by this process.  The results of these experiments are saved under the category of "CNN embeddings"</p></br>

<h3>17_COLAB_cnn_w2v</h3>
<p>Pre-trained 300d word2vec word embeddings are loaded using the "chakin" module.  The training and evaluation datasets are tokenized and the corresponding embedding is matched to each token in each tweet.  An array for each tweet is then created by stacking the vectors on top of each other.  Each array for each tweet has dimensions 40 x 300.  If a tweet has less than 40 tokens then the remaining rows of its array are filled with zeros.  A CNN is then trained and evaluated using the corresponding arrays that have been constructed for each instance by this process.  The results of these experiments are saved under the category of "CNN embeddings"</p></br>

<h2>RNN Embeddings experiments</h2>
<h3>18_COLAB_rnn_w2v</h3>
<p>300d word2vec embeddings are obtained from the "chakin" module.  The vocabulary of tokens in the twitter dataset is then used to create an embedding layer for input to the GRU RNN.  Each token in each tweet is then assigned a number depending on the position of its corresponding embedding in the embedding layer.  The GRU RNN is created with a many-to-one architecture and training is performed over 25 epochs prior to evaluation.  The results of these experiments are saved under the category of "RNN embeddings".<p></br>

<h3>19_COLAB_rnn_glove</h3>
<p>300d GloVe embeddings are obtained from the "chakin" module.  The vocabulary of tokens in the twitter dataset is then used to create an embedding layer for input to the GRU RNN.  Each token in each tweet is then assigned a number depending on the position of its corresponding embedding in the embedding layer.  The GRU RNN is created with a many-to-one architecture and training is performed over 25 epochs prior to evaluation.  The results of these experiments are saved under the category of "RNN embeddings".<p></br>

<h3>20_COLAB_rnn_fasttext</h3>
<p>300d FastText embeddings are obtained from the "chakin" module.  The vocabulary of tokens in the twitter dataset is then used to create an embedding layer for input to the GRU RNN.  Each token in each tweet is then assigned a number depending on the position of its corresponding embedding in the embedding layer.  The GRU RNN is created with a many-to-one architecture and training is performed over 25 epochs prior to evaluation.  The results of these experiments are saved under the category of "RNN embeddings".<p></br>

<h2>BERT experiments</h2>
<h3>21_COLAB_bert_large_classifier</h3>
<p>The pretrained large BERT model is loaded from the tensorflow hub (Google).  The training and evaluation datasets are tokenized by the BERT tokenizer.  The training set is then fed into the BERT model and it is fine tuned over 3 epochs.  The evaluation dataset is then fed into the fine tuned model and the predictions are compared against the actual values to create metrics.  The results of these experiments are saved under the cetagory of "BERT".<p></br>

<h3>22_COLAB_bert_base_classifier</h3>
<p>The pretrained base BERT model is loaded from the tensorflow hub (Google).  The training and evaluation datasets are tokenized by the BERT tokenizer.  The training set is then fed into the BERT model and it is fine tuned over 3 epochs.  The evaluation dataset is then fed into the fine tuned model and the predictions are compared against the actual values to create metrics.  The results of these experiments are saved under the cetagory of "BERT".<p></br>

<h2>10 folds cross validation results</h2>
<h3>23_COLAB_bert_large_assess</h3>
<p>10 folds cross validation is performed over the entire dataset using the large BERT model in order to obtain 10 sets of results.  These results are to be used in a statistical test to comapre BERT against GRU RNN</p></br>

<h3>24_COLAB_rnn_glove_assess</h3>
<p>10 folds cross validation is performed over the entire dataset using the GRU RNN model with a 300d GloVe input embedding array in order to obtain 10 sets of results.  These results are to be used in a statistical test to comapre BERT against GRU RNN</p></br>

<h2>Experiment results colation</h2>
<h3>25_COLAB_github_save</h3>
<p>All of the results for the "COLAB" experiments were saved initially in Google Cloud Storage.  The code in this file extracts all of the results and saves them to this GitHub repository as 'results/metrics_colab.pickle'<p></br>

<h2>Findings</h2>
<h3>26_analysis</h3>
<p>This code is used to generate the analysis charts and tables in section 5 (Findings) of the dissertation</p></br>
