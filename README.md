# entity-recognition-problem

## Exploratory Data Analysis:
I plotted label vs frequency graphs and most frequent words.<br>
We can see that the data is very imbalanced. <br><br><br>
## Feature Extraction:
1. **Orthographic Vector:** <br>
It basically checks on each position of the string, what character is present. <br>
'x' - > ASCII characters, <br>
'c' -> Small letters, <br>
'C' -> Capital letters, <br>
'n' -> Numbers, <br>
'p' -> Punctuations<br><br>
For example [['Hi, my name is Devashish'], ['How are you?']] will return [['CcpxccxccccxccxCcccccccc'], ['Cccxcccxcccp']]<br><br>
Then after getting the mapping, it creates a vector for it.
[[2, 1, 4, 0, 1, 1, 0, 1, 1, 1, 1, 0, 1, 1, 0, 2, 1, 1, 1, 1], [0, 0, 0, 0, 0, 0, 0, 0, 2, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 4]]<br>
'x' -> 0, <br>
'c' -> 1,<br>
'C' -> 2,<br>
'n' -> 3,<br>
'p' -> 4,<br><br>
The max length of this vector is 20 and it replaces characters from ortho mapping with numbers on that position of the string.<br><br>

2. **Pos Tag: **<br>
Position of Speech Tags.<br>
For this I am using nltk.pos_tag.<br><br>
Here are the definitions of each tags:<br>
CC -> coordinating conjunction<br>
CD -> cardinal digit<br>
DT -> determiner<br>
EX -> existential there (like: “there is” … think of it like “there exists”)<br>
FW -> foreign word<br>
IN -> preposition/subordinating conjunction<br>
JJ -> adjective ‘big’<br>
JJR -> adjective, comparative ‘bigger’<br>
JJS -> adjective, superlative ‘biggest’<br>
LS -> list marker 1)<br>
MD -> modal could, will<br>
NN -> noun, singular ‘desk’<br>
NNS -> noun plural ‘desks’<br>
NNP -> proper noun, singular ‘Harrison’<br>
NNPS -> proper noun, plural ‘Americans’<br>
PDT -> predeterminer ‘all the kids’<br>
POS -> possessive ending parent’s<br>
PRP -> personal pronoun I, he, she<br>
PRP -> possessive pronoun my, his, hers <br>
RB -> adverb very, silently, <br>
RBR -> adverb, comparative better <br>
RBS -> adverb, superlative best<br>
RP -> particle give up TO, to go ‘to’ the store.<br>
UH -> interjection, errrrrrrrm<br>
VB -> verb, base form take<br>
VBD -> verb, past tense took<br>
VBG -> verb, gerund/present participle taking<br>
VBN -> verb, past participle taken<br>
VBP -> verb, sing. present, non-3d take<br>
VBZ -> verb, 3rd person sing. present takes<br>
WDT -> wh-determiner which<br>
WP -> possessive wh-pronoun whose<br>
WRB -> wh-abverb where, when<br><br>

## Solutions:
1. **Method 1: Normal Classification:**<br>
I am using support vector classification for this.<br>
Evaluation: Recall is very low and we have many false negatives<br><br>
2. **Method 2: Downsampled Classification:**<br>
Since we are dealing with imbalanced data, I have downsampled the class with label('O') and am using support vector classification for this.<br>
Evaluation: Accuracy is very less.<br><br>
3. **Method 3: Upsampled Classification:**<br>
Since we are dealing with imbalanced data, I have upsampled the minority and am using naive bayes classification because the number of samples increases for this.<br>
Evaluation: Accuracy is very less.<br><br>
4. **Method 4: Penalized Classification:**<br>
Since we are dealing with imbalanced data, I tried penalizing the training model. I used SVC for this with class_weight='balanced'. But it took forever to run so I decided to skip this.<br>
Evaluation: Too much time.<br><br>
5. **Method 5: Random Forest:**<br>
Since the data in imbalanced, random forest is a good algorithm for this.<br>
Evaluation: It performed better than all the other models till now. It was able to classify other labels also.<br><br>
6. **Method 6: Reading text as tweets:**<br>
For this, I refered to http://noisy-text.github.io/2017/pdf/WNUT19.pdf.<br>
I have implemented a multi task neural network that aims at generating named entities in user generated text. <br>
The model captures some orthographic features at the character level by using convulation neural network, PoS features by using Bidirectional Long-Short Term Memory.Once the network is trained, I used it as a feature extractor to feed a Conditional Random Fields (CRF) classifier.
<br><br>
For example: Lets consider the two texts 'Hi Paris' and 'Welcome to Paris'. <br>
Here in the first text, 'Paris' can be reffered as Name entity and in the second text, 'Paris' can be reffered as Location entitity. <br>
This gave me an idea that the outputs from the previous word/layer makes difference in identifying the entity.
<br>
Evaluation: It performed decent and as it made the most sense, I decided to go with this.<br><br><br>


## Evaluation: 
For evaluation, since this is very imbalanced data, I have used confusion matrix to check false negatives. I have also used accuracy for some cases.<br>
**All the models had many false negatives. For future improvement, I should extract some more features. ** <br><br>


## Bonus:
For bonus, I clustered the predicted labels based on their probabilities. I used KMeans clustering.


## Some more insights:
Top likely transitions:<br>
B-person -> I-person 3.936851<br>
B-location -> I-location 2.291192<br>
I-product -> I-product 1.705511<br>
O      -> O       1.632621<br>
B-creative-work -> I-creative-work 1.417550<br>
I-creative-work -> I-creative-work 1.386468<br>
B-group -> I-group 1.333125<br>
B-product -> I-product 1.057185<br>
I-location -> I-location 0.951327<br>
I-group -> I-group 0.726022<br>
O      -> B-person 0.531396<br>
B-corporation -> I-corporation 0.516798<br>
O      -> B-location 0.494253<br>
O      -> B-group 0.371175<br>
O      -> B-corporation 0.290107<br>
<br><br>
Top unlikely transitions:<br>
B-location -> B-person -0.140064<br>
B-person -> B-location -0.142025<br>
B-group -> O       -0.152444<br>
B-person -> B-person -0.153057<br>
I-group -> O       -0.157578<br>
I-creative-work -> O       -0.238135<br>
I-product -> O       -0.587993<br>
B-product -> O       -0.602447<br>
B-creative-work -> O       -1.781312<br>
O      -> I-corporation -2.033485<br>
O      -> I-group -2.725038<br>
O      -> I-product -3.081457<br>
O      -> I-creative-work -3.115185<br>
O      -> I-location -3.260909<br>
O      -> I-person -3.946296<br>