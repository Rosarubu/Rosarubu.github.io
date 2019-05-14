# python文本相似度

reference:

[用Python进行简单的文本相似度分析](https://blog.csdn.net/xiexf189/article/details/79092629 )

[推荐系统技术文本相似性计算（三）实战篇](https://segmentfault.com/a/1190000005599507)

[Gensim Tutorial – A Complete Beginners Guide](https://www.machinelearningplus.com/nlp/gensim-tutorial/)

基本步骤：

1. 分词
2. 词典&语料库构建
3. optional: 使用tfidf weighted word2vec向量表示文本向量
4. 计算相似度

## 分词

基本中文就jieba英文就nltk

```python
'''简单来说以下就ok jieba.cut可以换成自定义的函数做多些处理'''
import jieba
all_doc_list = []
for doc in all_doc:
    doc_list = [word for word in jieba.cut(doc)]
    all_doc_list.append(doc_list)
    
>> [['a','b'],['c','d']]
```

其他：并行计算，加载用户词典，繁简体转换，小写转换，去停用词，标点去除，检查中英文

```python
import jieba
jieba.enable_parallel(4) # 开启并行分词模式，参数为并行进程数 windows不支持
jieba.load_userdict(file_name) # file_name 为文件类对象或自定义词典的路径
'''
词典格式和 dict.txt 一样，一个词占一行；每一行分三部分：词语、词频（可省略）、词性（可省略），用空格隔开，顺序不可颠倒。file_name 若为路径或二进制方式打开的文件，则文件必须为 UTF-8 编码
创新办 3 i
云计算 5
凱特琳 nz
台中
'''

'''繁体中文转换为简体中文 
先繁体转简体再分词与分词再繁简转换结果不一样，在分词前转'''
content='上海是一个好地方'
text = zhconv.convert(content.strip(), 'zh-hans')
content_seg = jieba.cut(content)#对一句话分词，返回list
>>['上海', '是', '一个', '好', '地方']

'''转化为小写'''
content_seg = [x.lower() for x in content_seg]
'''去停用词 停用词保存在list'''
content_seg = [x for x in content_seg if x not in stopwords]
'''去标点符号'''
punctuations = '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~＂＃＄％＆＇（）＊＋，－／：；＜＝＞＠［＼］＾＿｀｛｜' \'｝～｟｠｢｣､\u3000、〃〈〉《》「」『』【】〔〕〖〗〘〙〚〛〜〝〞〟〰〾〿–—‘’‛“”„‟…‧﹏﹑﹔·！？｡'
pattern = re.compile(r'^[%s\s\n]*$'%(re.escape(punctuations)))
result = []
for c in content_seg:
    c = re.sub(pattern,'',c)
    if len(c) > 1:
        result.append(c)
        
        
'''检查中英文'''
def check_contain_chinese(check_str):
    #True -> chinese
    for ch in check_str:
        if u'\u4e00' <= ch <= u'\u9fff': return True
    return False
```



## 词典&语料库构建

构建词典，bag-of-word模型计算每个词出现次数,计算词的tfidf

```python
from gensim.corpora import Dictionary
from gensim.models.tfidfmodel import TfidfModel
docs_dict = Dictionary(all_doc_list)
'''Filter out tokens in the dictionary by their frequency'''
docs_dict.filter_extremes(no_below=5, no_above=0.2)
'''
过滤之后可能有些词会缺失，这里重新建立词典索引
Assign new word ids to all words, shrinking any gaps
'''
docs_dict.compactify()
'''建立语料库[[(词索引id,出现频次),(),()],[(),()]]'''
docs_corpus = [docs_dict.doc2bow(doc) for doc in all_doc_list]
>>[[(0, 1), (1, 1), (2, 1), (3, 1)],
[(0, 1), (4, 1), (5, 1), (6, 1), (7, 1)]]

#id2word ({dict, Dictionary}, optional) – Mapping token - id, that was used for converting input data to bag of words format.
model_tfidf = TfidfModel(docs_corpus, id2word=docs_dict)
docs_tfidf = model_tfidf[docs_corpus]
docs_tfidf[docs_corpus[0]]
>>	[(0, 0.08112725037593049), 
    (1, 0.3909393754390612), 
    (2, 0.5864090631585919), 
    (3, 0.3909393754390612)]

#Convert a document in Gensim bag-of-words format into a dense numpy array
sparse2full(docs_tfidf[i], len(docs_dict))
```



## 计算相似度

```python
from gensim import similarities
#内存够用的情况下
index = similarities.SparseMatrixSimilarity(docs_tfidf[docs_corpus],
                                            num_features=len(docs_dict.keys()))
sim = index[docs_tfidf[docs_corpus[0]]
>>array([ 0.54680777, 0.01055349, 0. , 0.17724207, 0.17724207, 
0.01354522, 0.01279765, 0.70477605], dtype=float32)
#排序
sorted(enumerate(sim), key=lambda item: -item[1])
            
self.index = Similarity(self.index_path, corpus=doc_vec,
                        num_features=self.embedding_mt.shape[1],
                 		shardsize=32768)
```



## 使用tfidf weighted word2vec向量表示文本向量

```python
#word2vec训练
'''LineSentence: Iterate over a file that contains sentences: one line = one sentence. Words must be already preprocessed and separated by whitespace'''
sentences = LineSentence(trainFile)
model=word2vec.Word2Vec(sentences,min_count=3,size=100,workers=4)
model.save(modelSaveFile)
#load model
model = word2vec.Word2Vec.load(modelSaveFile)
#增量训练
print('old model len:',len(model.wv.vocab))
model.build_vocab(LineSentence(newtrainFile), update=True)
model.train(LineSentence(newtrainFile),total_examples=model.corpus_count,
                 epochs=model.epochs)
print('new model len:',len(model.wv.vocab))
model.save(modelSaveFile)
#word2vec向量 取出docs_dict中所有词的word2vec向量
model = word2vec.Word2Vec.load(modelSaveFile)
emb_vecs = np.vstack([model.wv[docs_dict[i]] for i in range(len(docs_dict))])
#取出文档中词对应的tfidf 
docs_vecs = np.vstack([sparse2full(c, len(docs_dict)) for c in docs_tfidf])
docs_emb = np.dot(docs_vecs, tfidf_emb_vecs) 

'''
docs_dict docs_corpus docs_tfidf三者的词索引id都是一套，所以构建的矩阵可以直接乘

例如有10个文档，词典里有20个词，word2vec是100维向量
则docs_vecs为10行*20列
emb_vecs为20行*100列
docs_emb为10*100，每行代表对应文本用tfidf加权后的词向量
'''
```

## 相似度计算代码

from [Implementing the five most popular Similarity Measures in Python](http://bigdata-madesimple.com/implementing-the-five-most-popular-similarity-measures-in-python/)

```python
from math import*
from decimal import Decimal

class Similarity():

	""" Five similarity measures function """

	def euclidean_distance(self,x,y):

		""" return euclidean distance between two lists """

		return sqrt(sum(pow(a-b,2) for a, b in zip(x, y)))

	def manhattan_distance(self,x,y):

		""" return manhattan distance between two lists """

		return sum(abs(a-b) for a,b in zip(x,y))


	def minkowski_distance(self,x,y,p_value):
		
		""" return minkowski distance between two lists """

		return self.nth_root(sum(pow(abs(a-b),p_value) for a,b in zip(x, y)),p_value)

	def nth_root(self,value, n_root):

		""" returns the n_root of an value """

		root_value  = 1/float(n_root)
		return round (Decimal(value) ** Decimal(root_value),3)

	def cosine_similarity(self,x,y):

		""" return cosine similarity between two lists """

		numerator = sum(a*b for a,b in zip(x,y))
		denominator = self.square_rooted(x)*self.square_rooted(y)
		return round(numerator/float(denominator),3)

	def square_rooted(self,x):

		""" return 3 rounded square rooted value """

		return round(sqrt(sum([a*a for a in x])),3)


	def jaccard_similarity(self,x,y):

		""" returns the jaccard similarity between two lists """

		intersection_cardinality = len(set.intersection(*[set(x), set(y)]))
		union_cardinality = len(set.union(*[set(x), set(y)]))
		return intersection_cardinality/float(union_cardinality)
```

