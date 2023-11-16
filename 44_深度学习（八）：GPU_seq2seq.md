@[toc]
#  GPU并行计算原理
这边只进行总结以及讲解部分使用api，具体参见官网：

API 列表

```python
tf.debugging.set log_device_placement 某个变量分配在哪个设备上
tf.config.experimental.set_visible_devices 本进程可见的设备
tf.config.experimental.ist logical_devices 获取逻辑设备
tf.config.experimentalist physical_devices 获取物理设备
tf.config.experimental.set_memory_growth 用多少，设置多少
tf.config.experimental.VirtualDeviceConfiguration 建立逻辑分区
tf.config.set_soft_device_placement 自动把某个计算分配到某个设备上，不容易出错
```

## 分布式策略
对于现在的ai来说，其实最为重要的就是其分布式框架，这也是现在在崇尚开源的当今，那些公司不会开源出来的分布式框架，这边就只是简单介绍一下：

### MirroredStrategy
同步式分布式训练，适用于一机多卡情况，每个 GPU 都有网络结构的所有参数，这些参数会被同步，数据并行，Batch 数据切为 N 份分给各个 GPU
梯度聚合然后更新给各个 GPU 上的参数。这就导致了可能会有浪费速度的情况。

### CentralStorageStrategy
MirroredStrategy 的变种，参数不是在每个 GPU 上，而是存储在一个设备上CPU 或者唯一的 GPU 上，计算是在所有 GPU 上并行的。

### MultiWorkerMirroredStrategy
类似于 MirroredStrategy 适用于多机多卡情况，适用于多机多卡情况。
![[image/71e0680fd0011618359da224358f40cf_MD5.png]]
同步需要等待训练的慢的卡，这可能会造成一定的效率牺牲。

### TPUStrategy
与 MirroredStrategy 类似，使用在 TPU 上的策略。

### ParameterServerStrategy
异步分布式，更加适用于大规模分布式系统，机器分为 Parameter Server 和 worker 两类，Parameter server 负责整合梯度，更新参数，Worker 负责计算，训练网络。

![[image/89c141e2ed6e97e3a01805db336f2bbf_MD5.png]]
异步面临着会浪费效率的可能，需要时间戳的参与。


# seq2seq
在seq2seq当中成功应用了注意力机制，在机器翻译，和很多领域做到了很大的突破。
## 原理介绍
Seq2Seq其实就是Encoder-Decoder结构的网络，它的输入是一个序列，输出也是一个序列。在Encoder中，将序列转换成一个固定长度的向量，然后通过Decoder将该向量转换成我们想要的序列输出出来。

![[image/f66e1bb90cfcca9a726f72f1dc0d8883_MD5.png]]
例如上图就是一个比较经典的翻译模型的seq2seq图，我们可以明显看出，在这个seq2seq模型当中我们的模型分为两块，左边蓝色的和优辩红色的，蓝色的我们一般称之为encoder，红色的我们称之为decoder，encoder的输入是我们要翻译之前的sequence，输入第一层embedding层后，再往上层走，输入到RNN当中，然后输出的矩阵（attention weights）会和decoder的每次需要输出的相结合一起生成 context vector 最后用于生成我们要翻译的词汇。

这边解释不清楚，用代码进行解释吧，这边参照的是tf的高级项目英-西班牙的翻译。

## 代码讲解
### 包含的库

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
%matplotlib inline
import numpy as np
import pandas as pd
import tensorflow as tf
from tensorflow import keras
import unicodedata
import re
from sklearn.model_selection import train_test_split
```

### 数据初始化

```python
# 数据处理，主要和业务相关，当工作的时候绝大多数也就是将业务和代码相连接，这是十分重要的
# -----------------------------------------------------------------------------
# 数据预处理 去除重读的因子 和带空格以及特殊标点符号的东西
def preprocess_sentence(i):
  # NFD是转换方法，把每一个字节拆开，Mn是重音，所以去除
  i = ''.join(c for c in unicodedata.normalize('NFD',i) if unicodedata.category(c)!='Mn')
  # 把英文或者西班牙文变成纯小写，翻译与大写小写无关，降低影响,并且去掉头尾的空格
  i = i.lower().strip()
  # 在单词与跟在其后的标点符号之间插入一个空格
  i = re.sub(r"([?.!,¿])", r" \1 ", i)
  # 因为可能有多余空格，所以处理一下
  i = re.sub(r'[" "]+', " ", i)
  # 除了 (a-z, A-Z, ".", "?", "!", ",")，将所有字符替换为空格
  i = re.sub(r"[^a-zA-Z?.!,¿]+", " ", i)
  i = i.rstrip().strip()
  i = '<start> ' + i + ' <end>'
  return i

def load_dataset(path, num_examples):
  # lines就是读取之后的每一行的数据列表
  lines = open(path, encoding='UTF-8').read().strip().split('\n')
  # i就是一个样本，一个英文样本或者一个西班牙的样本
  word_pairs = [[preprocess_sentence(i) for i in line.split('\t')]for line in lines[:num_examples]]
  # 我们这边想要拿英文翻译成西班牙文，而我们查看data当中发现英语在左侧 西班牙语在右侧，所以英语左边应该是input
  inputs,targets =zip(*word_pairs)
  # Tokenizer帮我们把词语式的转换为id式的，filters是黑名单 英语和西班牙语的需要额外自己分别训练
  inputs_tokenizer = tf.keras.preprocessing.text.Tokenizer(filters='')
  targets_tokenizer = tf.keras.preprocessing.text.Tokenizer(filters='')
  # 训练词袋
  inputs_tokenizer.fit_on_texts(inputs)
  targets_tokenizer.fit_on_texts(targets)
  # 把英文或西班牙文转化为数字 sparse
  inputs_tensor = inputs_tokenizer.texts_to_sequences(inputs)
  targets_tensor = targets_tokenizer.texts_to_sequences(targets)
  # 还需要补长或者截断 没有maxlen 就是自动取最长的进行补0
  inputs_tensor = tf.keras.preprocessing.sequence.pad_sequences(inputs_tensor, padding='post')
  targets_tensor = tf.keras.preprocessing.sequence.pad_sequences(targets_tensor, padding='post')
  return input_tensor, target_tensor, inputs_tokenizer, targets_tokenizer

data_path = './data_spa_en/spa.txt'
# 需要训练的数据量 这边取全部，如果觉得训练太久，这边就选小一点
num_examples = 118964
# inp_lang targ_lang 是tokenizer
input_tensor, target_tensor, input_tokenizer, target_tokenizer = load_dataset(data_path, num_examples)
# 这边就不定义测试集了，对于nlp领域来说准确性一般没有什么作用
input_tensor_train, input_tensor_val, target_tensor_train, target_tensor_val = train_test_split(input_tensor, target_tensor, test_size=0.2)
batch_size = 64
embedding_dim = 256
units = 1024
# 训练集
dataset = tf.data.Dataset.from_tensor_slices((input_tensor_train,target_tensor_train)).shuffle(len(input_tensor_train))
dataset = dataset.batch(batch_size,drop_remainder=True)
# -----------------------------------------------------------------------------
```
### 构造encoder

```python
class Encoder(tf.keras.Model):
  def __init__(self,vocab_size,embedding_dim,units):
    super().__init__()
    self.embedding_dim = embedding_dim
    self.units = units
    self.vocab_size = vocab_size # 这个是train的字典的数目
    # 定义embedding层 需求的参数第一个参数是词典的数目 后者是要降成的维度
    self.embedding = keras.layers.Embedding(self.vocab_size,self.embedding_dim)
    # 定义GRU层 是LSTM的变种，units是h的维度 return_sequences是否输出上方的h，return_state是输出最后一个维度的stat
    self.gru = keras.layers.GRU(self.units,return_sequences=True,return_state=True,recurrent_initializer='glorot_uniform')

  def call(self, x):
    # 输入的x的维度数（batch-size，num-step） batch-size的意思样本数 num-step一个序列或者一个样本的词的数目 然后内部的词语是sparse
    # 调用encoder需要返回gru的h和state
    x = self.embedding(x)
    # 到这边x的形状变成了（batch-size，num-step，embedding-dim）
    output, state = self.gru(x)
    # 这边x的形状变成了（batch-size，num-step，units），自己想一下rnn的流程
    return output, state
```

### 注意力机制	Bahdanau
这边采用最早出现的注意力机制，熟悉一下，也十分的简单。
如果想要做机器翻译，建议还是使用cross-attention，这边给出相关的链接，这边不进行讲解。[链接](https://blog.csdn.net/vivi_cin/article/details/133361681)

```python
class BahdanauAttention(tf.keras.Model):
  def __init__(self, units):
    super().__init__()
    self.W1 = tf.keras.layers.Dense(units)
    self.W2 = tf.keras.layers.Dense(units)
    self.V = tf.keras.layers.Dense(1)
      
  def call(self, state, encoding_output):
    # encoding_output的形状是（batch-size，num-step，unints），state的形状是（batch-size，unints）
    # state扩增第一维度 此时是(batch-size,1,unints)
    state = tf.expand_dims(state, 1)
    # 广播相加后attention_weights的形状是(batch-size,num-step,1),加了层softmax将内部的数据转化为概率，且沿axis=1的方向进行概率调整
    attention_weights = tf.nn.softmax(self.V(tf.nn.tanh(self.W1(encoding_output) + self.W2(state))))
    # 先使用广播计算相乘，再计算均值 至此context_vector的形状是(batch-size,unints)
    context_vector = attention_weights * encoding_output
    context_vector = tf.reduce_sum(context_vector, axis=1)
    return context_vector, attention_weights
```
### 构造decoder

```python
class Decoder(tf.keras.Model):
  def __init__(self, vocab_size, embedding_dim, units, batch_size):
    super().__init__()
    self.batch_size = batch_size
    self.units = units
    self.embeddimg_dim = embedding_dim
    self.embedding = keras.layers.Embedding(vocab_size, self.embedding_dim)
    self.gru = keras.layers.GRU(self.units,return_sequences=True,return_state=True,recurrent_initializer='glorot_uniform')
    self.fc = keras.layers.Dense(vocab_size)
    self.attention = BahdanauAttention(self.units) #一般都是选择将encoding和decoding的unints的大小设置的一样

  def call(self, x, state, encoding_output):
    # x的形状是（batch-size，num-step）state的形状是（batch-size，units）
    x = self.embedding(x)
    # x输出之后就是（batch-size，num-step，units）
    # context_vector的大小是(batch-size,units) attention_weights的大小是(batch-size,num-step,1)
    context_vector, attention_weights = self.attention(state, encoding_output)
    # 把context（之前encoder计算出来的位置信息）和state（每次都会更新的前一个rnn输出的状态）进行拼接
    # 增加维度和按照最后一个维度进行拼接  此时x变成(batch-size,num-step,units+units)
    x = tf.concat([tf.expand_dims(context_vector, 1), x], axis=-1)
    output, state = self.gru(x)
    x = self.fc(output)
    # x的形状是（batch-size,num-step,vocab_size）
    return x, state, attention_weights
```
### 损失函数的构建

```python
loss_object = keras.losses.SparseCategoricalCrossentropy(from_logits=True)
def loss_function(real, pred):
    #是零的时候返回结果是True，因此要取反操作
    #tf.math.equal(real, 0)是padding的部分都是1，不是padding的部分都是零，因此我们要取反
    mask = tf.math.logical_not(tf.math.equal(real, 0))
    loss_ = loss_object(real, pred)
    #将张量转换为新类型,变为float类型
    mask = tf.cast(mask, dtype=loss_.dtype)
    #padding部分的mask是零
    loss_ *= mask
    #计算累计的损失平均
    return tf.reduce_mean(loss_)
```
### 一些别的定义

```python
optimizer = keras.optimizers.Adam()
checkpoint_dir = './8-1_checkpoints'
if not os.path.exists(checkpoint_dir):
    os.mkdir(checkpoint_dir)
checkpoint_prefix = os.path.join(checkpoint_dir, "ckpt")
encoder = Encoder(len(input_tokenizer)+1,embedding_dim,units)
decoder = Decoder(len(target_tokenizer)+1,embedding_dim,units)
checkpoint = tf.train.Checkpoint(optimizer=optimizer,encoder=encoder,decoder=decoder)
```
### 训练函数

```python
@tf.function
def train_step(input, target):
  loss = 0
  with tf.GradientTape() as tape:
    encoding_output, state = encoder(input)
    # 对于decoding一开始需要传入全start进去，也就是对应batch-size的start
    decoding_input = tf.expand_dims([target_tokenizer.word_index['<start>']] * batch_size, 1)
    # 直到输入到最大上限的字数跳出
    for t in range(1, target.shape[1]):
      predictions, state, _ = decoder(decoding_input, state, encoding_output)
      # 可以看出我们这边是一列一列进行的训练 值得关注的是训练的时候我们是把正确数据拿去训练的 和测试的时候不一样,测试的时候是生成的再输入，一轮一轮来的
      loss += loss_function(target[:, t], predictions)
      decoding_input = tf.expand_dims(target[:, t], 1)
        
  # 这里是每个batch上平均的损失函数 用于显示
  batch_loss = (loss / int(target.shape[1]))
  variables = encoder.trainable_variables + decoder.trainable_variables
  # 求梯度
  gradients = tape.gradient(loss, variables)
  #有了梯度以后，可以用optimizer去做apply
  optimizer.apply_gradients(zip(gradients, variables))
  return batch_loss

epochs = 10
for epoch in range(epochs):
  start = time.time()
  total_loss = 0
  # 每次去取dataset.take(steps_per_epoch)这么多数据
  for (batch, (input, target)) in enumerate(dataset.take(batch_size)):
    batch_loss = train_step(input, target)
    total_loss += batch_loss
    if batch % 100 == 0:
        print('Epoch {} Batch {} Loss {:.4f}'.format(epoch + 1, batch, batch_loss.numpy()))
  if (epoch + 1) % 2 == 0:
      checkpoint.save(file_prefix = checkpoint_prefix)
  print('Epoch {} Loss {:.4f}'.format(epoch + 1, total_loss / batch_size))
  print('Time taken for 1 epoch {} sec\n'.format(time.time() - start))
```


### 模型保存以及使用
[链接](https://blog.csdn.net/qq_44961869/article/details/122489627)

参考这个即可，这个讲解的十分详细。


### 使用翻译

```python
# 接收字符串，并进行翻译
def translate(sentence):
  # 定义一个关系图
  attention_plot = np.zeros((max_target_length,max_input_length))
  # 对输入的语言做和训练时同样的预处理后，再进行word转id
  sentence = preprocess_sentence(sentence)
  # text到id的转换
  inputs = [input_tokenizer.word_index[i] for i in sentence.split(' ')]
  # 加padding
  inputs = keras.preprocessing.sequence.pad_sequences([inputs], maxlen=max_input_length, padding='post')
  # 转换为tf张量
  inputs = tf.convert_to_tensor(inputs)
  # 结果先初始化一个空串
  result = ''
  encoding_out, state = encoder(inputs)
  # 第一次输入start进去
  decoding_input = tf.expand_dims([target_tokenizer.word_index['<start>']], 1)
  print(decoding_input)
  for t in range(max_target_length):
    predictions, state, attention_weights = decoder(decoding_input, state, encoding_out)
    # 对attention_weights进行处理得到相关的关系图
    attention_weights = tf.reshape(attention_weights, (-1,))
    attention_plot[t] = attention_weights.numpy()  # 为了画图
    
    predicted_id = tf.argmax(predictions[0],axis=1).numpy()[0]
    # print(predicted_id)


    # print(target_tokenizer.index_word[1])
    # 通过id知道单词
    result += target_tokenizer.index_word[predicted_id] + ' '

    # 终止循环
    if target_tokenizer.index_word[predicted_id] == '<end>':
        break
    decoding_input = tf.expand_dims([predicted_id], 1)

  print('Input: %s' % (sentence))
  print('Predicted translation: {}'.format(result))

  # 因为输出不一定有输入的长度长，也就是result长度小于输入的长度
  attention_plot = attention_plot[:len(result.split(' ')), :len(sentence.split(' '))]

  # 绘出关系图
  fig = plt.figure(figsize=(10,10))
  ax = fig.add_subplot(1, 1, 1)
  ax.matshow(attention_plot, cmap='viridis')
  fontdict = {'fontsize': 14}
  #把标注写上，我们需要把第零个位置空出来，看图即可看出
  ax.set_xticklabels([''] + sentence.split(' '), fontdict=fontdict, rotation=90)
  ax.set_yticklabels([''] + result.split(' '), fontdict=fontdict)

  plt.show()

translate(u'hace mucho frio aqui.')
```

