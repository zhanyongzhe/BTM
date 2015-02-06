# Code of Biterm Topic Model

Biterm Topic Model (BTM) is a word co-occurrence based topic model that learns topics by modeling word-word co-occurrences patterns (e.g., biterms).
(In constrast, LDA and PLSA are word-document co-occurrence topic models, since they model word-document co-occurrences.)

A biterm consists of two words co-occurring in the same context, for example, in the same short text window. Unlike LDA models the word occurrences, BTM models the biterm occurrences in a corpus. In generation procedure, a biterm is generated by drawn two words independently from a same topic. In other words, the distribution of a biterm b=(wi,wj) is defined as:

       P(b) = \sum_k{P(wi|z)*P(wj|z)*P(z)}.

With Gibbs sampling algorithm, we can learn topics by estimate P(w|k) and P(z).

More detail can be referred to the following paper:

> Xiaohui Yan, Jiafeng Guo, Yanyan Lan, Xueqi Cheng. A Biterm Topic Model For Short Text. WWW2013.

## Usage ##

The code includes a runnable example, you can run it by:

       $ script/runExample.sh

It trains BTM over the documents in *sample-data/doc\_info.txt* and output the topics. The doc\_info.txt contains all the training documents, where each line represents one document with words separated by space as:
> word1 word2 word3 ....

(*Note: the sample data is only used for illustration of the usage of the code. It is not the data set used in the paper.*)

You can change the paths of data files and parameters in *script/runExample.sh* to run over your own data. 

Indeed, the *runExample.sh* processes the input documents in 4 steps.

**1. Index the words in the documents**   
   To simplify the main code, we provide a python script to map each word to a unique ID (starts from 0) in the documents. 

    $ python script/indexDocs.py <doc_pt> <dwid_pt> <voca_pt>
    	doc_pt    input docs to be indexed, each line is a doc with the format "word word ..."
    	dwid_pt   output docs after indexing, each line is a doc with the format "wordId wordId ..."
    	voca_pt   output vocabulary file, each line is a word with the format "wordId     word"

**2. Topic learning**  
   The next step is to train the model using the documents represented by word ids.    

    $ ./src/btm est <K> <W> <alpha> <beta> <n_iter> <save_step> <docs_pt> <model_dir> 
      K	int, number of topics
      W	int, size of vocabulary
      alpha	double, Symmetric Dirichlet prior of P(z), like 1
      beta	double, Symmetric Dirichlet prior of P(w|z), like 0.01
      n_iter	int, number of iterations of Gibbs sampling
      save_step	int, steps to save the results
      docs_pt	string, path of training docs
      model_dir	string, output directory
 
   The results will be written into the directory "model\_dir":   
   - k20.pw_z: a K*M matrix for P(w|z), suppose K=20   
   - k20.pz:   a K*1 matrix for P(z), suppose K=20

**3. Inference topic proportions for documents, i.e., P(z|d)**     
   If you need to analysis the topic proportions of each documents, just run the following common to infer that using the model estimated.

    $ ./src/btm inf <type> <K> <docs_pt> <model_dir>
      K	int, number of topics, like 20
      type	 string, 4 choices:sum_w, sum_b, lda, mix. sum_b is used in our  paper.
      docs_pt	string, path of docs to be inferred
      model_dir	string, output directory

   The result will be output to "model_dir":   
   - k20.pz_d: a N*K matrix for P(z|d), suppose K=20
  
**4. Results display**    
   Finally, we also provide a python script to illustrate the top words of the topics and their proportions in the collection. 

    $ python script/topicDisplay.py <model_dir> <K> <voca_pt>
      model_dirthe output dir of BTM
      Kthe number of topics
      voca_ptthe vocabulary file

## Related codes ##
- [Online BTM](https://github.com/xiaohuiyan/OnlineBTM)
- [Bursty BTM](https://github.com/xiaohuiyan/BurstyBTM)

## History ##
- 2015-01-12, v0.5, improve the usability of the code
- 2012-09-25, v0.1

If there is any question, feel free to contact: [Xiaohui Yan](http://shortext.org "Xiaohui Yan")(xhcloud@gmail.com).