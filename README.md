SolrPlugins
======================

A repository for Dice.com's Solr plugins. This relies on the solr source so please note the APACHE license.

Please see https://github.com/DiceTechJobs/SolrConfigExamples for example solr config file entries to configure these plugins.

Included:

* Plugins necessary for **Conceptual Search** Implementation (see Lucene Revolution 2015 talk - http://lucenerevolution.org/sessions/implementing-conceptual-search-in-solr-using-lsa-and-word2vec/)
  * Custom query parsers: **VectorQParser** (for handling dense vector fields), **QueryBoostingQParser** (weighted synonym term expansion at query time)
  * Custom token filters - **MeanPayloadTokenFilter** (averages payloads over duplicate terms), **PayloadQueryBoostTokenFilter** (turns a payload in a synonym file into a term boost at query time)
  * See also https://github.com/DiceTechJobs/SolrConfigExamples for example solr xml files
  * See also https://github.com/DiceTechJobs/ConceptualSearch for python scripts to extract common keywords and phrases, train the word2vec model and cluster the resulting word vectors.
* Custom Token Filters
  * **TypeEraseFilter** - erases the type field value from the tokens in an analysis chain. Useful if applying several sets of synonym filters, and you want to use only some of these filters to filter the resulting tokens with a TypeTokenFilterFactory
  * **ConstantTokenFilter** - emits a constant token for each token in the token stream. Useful for doing things like counting certain token types - use a synonym filter plus a TypeTokenFilter to filter to certain tokens, and then a ConstantTokenFilter to allow counting or boosting by the number of tokens using the termfreq() function at query time (or apply a negative boost using the count and the div function).
* **Dice custom MLT handler**
  * Allows top n terms per field, rather than across all fields specified
  * Fleshes out 'more like these' functionality - use multiple target documents to generate recommendations
  * **boost** query support - supports multiplicative boosts for matching items, for instance boost by relevancy and proximity to your user
  * Better support for content streams in place of source documents to generate recommendations from
* **Unsupervised feedback handler**
  * Implements a well-researched methodology from the field of information retrieval for improving relevancy. Also known as 'blind feedback' and 'pseudo-relevancy feedback'.
  * Uses code based on the custom MLT handler to execute each query twice. The first execution uses the MLT code to grab the top terms for the result set by their tf.idf values. It then adds these terms to the original query (term expansion) and re-executes.
  * This 2 phase execution happens inside of solr (one round trip) and so has a negilible impact on resonse time for most queries while noticeably improving relevancy.
* **DiceMultipleCaseSuggester** 
  * Solr suggester modification - can handle UPPER, lower and **T**itle **C**ase variations for type ahead. 
  * Regular solr suggester functionality is case sensitive.
* **DiceSpellCheckComponent** and **DiceDirectSolrSpellChecker**
  * Regular solr spell check component can only search for corrections within 2 edit distances of each query term
  * This extends this functionality to allow you to embed a file of common user typos that will take precedence over the edit distance matches.
  *  Allows you to datamine common typos that go beyond an edit distance of two and inject them into your spellchecker.
