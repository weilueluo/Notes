## Documents & Data on the Web Coursework

> **Name**: weilue luo
> **ID**: 10404762
> **Date**: 16 Nov 2020

<div style="page-break-after: always; break-after: page;"></div>

[TOC]

<div style="page-break-after: always; break-after: page;"></div>

## Preface

I am using intellij with Java 8. There are more technical explanations in the code. I put many utilities functions / classes into the <code>BasicInvertedIndex</code> file, this is bad practice, but according to instruction I can only submit one java file.

## Functionality

### Features, Improvements & Problems

#### Stream

Highly readable, efficient, pipelined code using the <code>Stream</code> API from Java 8.

#### Raw Tokenize

It splits text to lines by:

- Any tab, carriage-return, newline, form-feed, double-quote, and single-quote.
- Any dot, question mark, exclamation mark that follows by space/tab/carriage-return/form-feed/newline.

I included quotes because they are common for quoting. Dot/Question mark/Exclamation mark must follow by delimiter for more precise match of end-of-sentence, e.g. it will not split statements like: $10.0\times 10!=\lambda$.

While it generate more suitable index term, it assumes usage of quotes which may not be true. Moreover, the tokenization is design for English, separators of other languages are not considered.

#### Tokenize From Line

It cleans the word and returns true if only the first letter is uppercase, making it more precise than simply lowercase it.

#### Expand Tokens

A consideration on multi-term/single term:

- **Number**: Keep original token because user may search using it.
- **Date**: Keep both original token and numeric form, because user may just enter number without separator to search for dates.
- **URL**: Break it into pieces by punctuations, because the user may not search the full URL.
- **Connected Word**: Keep each pieces as well, e.g. ease-of-access $\rightarrow$ [ease, of, access, easeofaccess, EOA].
- **Other**: Keep letters only (which is not good for other languages).

All tokens share the same position to ensure it reflects the actual position. This process may also generate blank token, but it will be removed in latter stage. Normally, letters-only tokens are added for more robust indexing terms.

#### Stem & Stopword Removal

Lemmatization and stopwords removal are achieve with stemming and <code>StopAnalyser</code>, like-terms are collapsed into one which make it more suitable for indexing, however, it may introduce over-stemming problem since it is a crude process.

#### Positional TF Term

- Used <code>Set</code> for positions, collapsing same expanded terms.
- The size of positions is term frequency.

#### Sorting

Each inverted-index is sorted using following heuristic:

$$
\text{ranking}=\text{tfidf}+\frac{\sqrt{\sigma+\lambda}}{\sigma+\lambda}
$$
where $\sigma$ is the variance of the positions and $\lambda$ is a constant to reduce score for low-occurrence-words.

- Not using standard-deviation because variance gives smoother step.
- I used $\lambda=20$ to dampen effect of single occurrence term and avoid division by zero.

<img src="D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118163620038.png" alt="sqrtx/x" style="zoom:50%;" />

**Example**

- **TFIDF**

  ![TFIDF](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118154329554.png)
  Although *Bart_the_lover* has less occurrence of the token "I", it is better than *Bart_the_Murderer* because those three words have very low variance, therefore the variance is getting too little weight here.

- **My heuristic (Standard Deviation)**

  ![STDDEV](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118154523202.png)

  By using standard deviation heuristic, *Bart_the_lover* raised to the first place, but clearly over-done, because although *Bart_the_Fink* has higher variance, it has low variance with positions: 1983, 2008 and 2034, therefore the variance is getting too much weighting here.

- **My heuristic (Variance)**

  ![Variance](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118154648301.png)
  By using variance heuristic, *Bart_the_Lover* got the optimal position, thus this heuristic is most suitable among three variances.

This heuristic work well in most cases except e.g.:

- Single occurrence term, because it has 0 variance which made it score very high.
  - To dampen this effect you can assign a higher $\lambda$ (e.g. 20).
  - Another options would be make use of the number of positions where less positions gets lower weighting.
- Short documents, $\frac{\sqrt{\sigma}}{\sigma}$ will be very steep which made it sensitive to variance, resulting in overly-high difference.

### Other Limitations

- Lack of language support.

- Lack of document type support, it assumes the input are plain text, cannot handle images, videos, etc.

- The handling of special tokens are not robust enough.

  - Some unhandled token includes: measures, citations, hashtags.
  
- No check for spelling, assumed document text is correct, but this shouldn't have much impact.

- The inverted index is sorted alphabetically which may not be helpful.

## Performance

### Design Patterns & Effects

The overall design pattern is MapReduce, which is optimized for distributed computing. Obviously, this slows down performance since we only have one computer with little data, but for the purpose of coursework it is fine.

I made heavy use of features such as <code>Stream</code>, <code>lambda expression</code>, <code>method references</code> to chain operations to achieve high efficiency.

#### Positional TF Term

It is used to group all relevant data for term, and provide useful utilities such as merging. It follows the immutability and factory design pattern which made it safer and easier to use.

#### Stopword Removal

I used <code>Set</code> instead of <code>List</code> in <code>StopAnalyser</code> for faster check.

#### In-mapper Aggregation

An profile analysis using Java Flight Recorder revealed that <code>map</code> and <code>reduce</code> functions took 15.4% and 3.8% respectively and overall runtime of 4.52s.

![mapper-runtime](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118205725088.png)

![image-20201118205857852](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118205857852.png)

Without in-mapper aggregation, the <code>map</code> and <code>reduce</code> functions took 16.3% and 11.6% respectively and overall runtime of 10.011s.

![image-20201118213331428](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118213331428.png)

![image-20201118213210884](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201118213210884.png)

From above we can see my in-mapper aggregation has significant impact on the performance.

### Other Limitations

#### Map Reduce

- Reduce/Sorting is take very long time if map stage generate too many keys.
- It is built for batch processing so not useful for interactive job.
- It is not suitable for tasks that have dependency on each other as they cannot be parallelize.

#### Stream

It can create unnecessary operations and less flexible than loops.

#### Expand Tokens

If we were to run this on huge datasets, the bottleneck will be expand token process as it takes comparatively long time to check for special tokens (which is not in most cases).

## Appendix

### Word Count

Excluding appendix, word count is 978: ![word-count](D:\UOM\Projects\Notes\docs_cwk.assets\image-20201119103553757.png).

### 50 lines output (1700 words)

