# Scoring Sentence Singletons and Pairs for Abstractive Summarization
Code and Data for the ACL 2019 paper **"[Scoring Sentence Singletons and Pairs for Abstractive Summarization](https://arxiv.org/abs/1906.00077)"**

# Data

Our data consists of > 1 million **sentence fusion** instances, of the form: 

    Input: one or two articles sentences

    Output: the summary sentence formed by compressing/fusing the input sentences

Our data is derived from existing summarization datasets: CNN/Daily Mail, XSum, and DUC-04.

# Generating the data

To generate the data (currently, we only provide CNN/Daily Mail), follow the following steps:

1) Download the zip files from https://github.com/JafferWilson/Process-Data-of-CNN-DailyMail. Unzip and place `cnn_stories_tokenized/`, `dm_stories_tokenized/`, and `url_lists/` inside the `./data/cnn_dm_unprocessed/` directory.
2) Run the following code:

    ```
    python create_cnndm_dataset.py
    python highlight_heuristic.py --dataset_name=cnn_dm
    ```

Four files will appear in the newly-created ./data/cnn_dm_processed directory. Each of the .tsv files will have the same number of lines.

* articles.tsv
    * 1 article per line, tab-separated list of sentences

* summaries.tsv
    * 1 summary per line, tab-separated list of sentences

* highlights.tsv
    * 1 set of fusion examples per line, tab-separated list of fusion examples. The number of fusion examples corresponds to the same number of summary sentences in the same line in summaries.tsv. Each fusion example is either 0, 1, or 2 source indices (separated by ",") representing the which sentences from the article were fused together to form the summary sentence.

* pretty_html.html
    * Examples highlighting which sentences were fused together to form summary sentences


## Code and models coming soon!

python run_summarization.py --mode=train --dataset_name=cnn_dm --dataset_split=train --exp_name=cnn_dm --max_enc_steps=100 --min_dec_steps=10 --max_dec_steps=30 --single_pass=False --batch_size=128 --num_iterations=10000000 --by_instance

python run_summarization.py --mode=eval --dataset_name=cnn_dm --dataset_split=val --exp_name=cnn_dm --max_enc_steps=100 --min_dec_steps=10 --max_dec_steps=30 --single_pass=False --batch_size=128 --num_iterations=10000000 --by_instance

python run_summarization.py --mode=train --dataset_name=cnn_dm --dataset_split=train --exp_name=cnn_dm --max_enc_steps=100 --min_dec_steps=10 --max_dec_steps=30 --single_pass=False --batch_size=128 --num_iterations=10000000 --by_instance --restore_best_model


python preprocess_for_bert_article.py --dataset_name=cnn_dm

python bert/extract_features.py --dataset_name=cnn_dm --layers=-1,-2,-3,-4 --max_seq_length=400 --batch_size=1

python run_classifier.py --task_name=merge --do_train --do_eval --dataset_name=cnn_dm --num_train_epochs=1000.0

python run_classifier.py --task_name=merge --do_predict=true --dataset_name=cnn_dm

python bert_scores_to_summaries.py --dataset_name=cnn_dm

python sentence_fusion.py --dataset_name=cnn_dm --use_bert=True