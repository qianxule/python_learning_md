@[toc]

# data模块的使用
在训练的过程中，当数据量一大的时候，我们纯读取一个文件，然后每次训练都调用相同的文件，然后进行处理是很不科学的，或者说，当我们需要进行多次训练的时候，我们实际上可以将数据先切分，打乱到对应的位置，然后存储到文件夹当中，下次读取然后进行训练。这样子也可以避免一下子加载太多的数据。（这对于大数据的图像切割领域尤其重要）

## 基础api的介绍

```python
import numpy as np
import pandas as pd
import tensorflow as tf

# 经过下面的使用得到的dataset可以当作是迭代器 或者 就是数据提取器

# from_tensor_slices数据切片 返回的是一个迭代器 需要for去取 
dataset = tf.data.Dataset.from_tensor_slices(np.arange(3))
for item in dataset:
    print(item)
print('-'*50)

# from_tensors 不对数据进行处理 就是一个长数据 但是一样得用for去取数据
dataset = tf.data.Dataset.from_tensors(np.arange(3))
for item in dataset:
    print(item)
print('-'*50)

# batch(2)一次性取多少数据
dataset = tf.data.Dataset.from_tensor_slices(np.arange(10)).batch(4)
for item in dataset:
    print(item)
print('-'*50)

# repeat把数据进行复制处理
dataset = tf.data.Dataset.from_tensor_slices(np.arange(2)).repeat(2)
for item in dataset:
    print(item)
print('-'*50)

# interleave 就是提取数据的方式 和下面进行对比
dataset = tf.data.Dataset.from_tensor_slices(np.arange(10)).batch(2)
dataset2 = dataset.interleave(
    lambda v: tf.data.Dataset.from_tensors(v).repeat(3), # map_fn
    cycle_length = 3, # cycle_length,每一个cycle提取的个数
    block_length = 2, # block_length
)
for item in dataset2:
    print(item)

# interleave 就是提取数据的方式
dataset = tf.data.Dataset.from_tensor_slices(np.arange(10)).batch(2)
dataset2 = dataset.interleave(
    lambda v: tf.data.Dataset.from_tensor_slices(v).repeat(3), # map_fn
    cycle_length = 3, # cycle_length,竖选3个为一个cycle
    block_length = 2, # block_length，横选2个
)
for item in dataset2:
    print(item)
print('-'*50)


x = np.array([[1, 2], [3, 4], [5, 6]])
y = np.array(['cat', 'dog', 'fox'])
#输入的参数是元祖的情况下
dataset3 = tf.data.Dataset.from_tensor_slices((x, y))
print(dataset3)

for item_x, item_y in dataset3:
    print(item_x, item_y)
#输入的参数是字典的情况下
dataset4 = tf.data.Dataset.from_tensor_slices({"feature1": x,"label": y})
print(dataset4)
for item in dataset4:
    print(item["feature1"], item["label"])
```
输出：

```python
tf.Tensor(0, shape=(), dtype=int64)
tf.Tensor(1, shape=(), dtype=int64)
tf.Tensor(2, shape=(), dtype=int64)
--------------------------------------------------
tf.Tensor([0 1 2], shape=(3,), dtype=int64)
--------------------------------------------------
tf.Tensor([0 1 2 3], shape=(4,), dtype=int64)
tf.Tensor([4 5 6 7], shape=(4,), dtype=int64)
tf.Tensor([8 9], shape=(2,), dtype=int64)
--------------------------------------------------
tf.Tensor(0, shape=(), dtype=int64)
tf.Tensor(1, shape=(), dtype=int64)
tf.Tensor(0, shape=(), dtype=int64)
tf.Tensor(1, shape=(), dtype=int64)
--------------------------------------------------
tf.Tensor([0 1], shape=(2,), dtype=int64)
tf.Tensor([0 1], shape=(2,), dtype=int64)
tf.Tensor([2 3], shape=(2,), dtype=int64)
tf.Tensor([2 3], shape=(2,), dtype=int64)
tf.Tensor([4 5], shape=(2,), dtype=int64)
tf.Tensor([4 5], shape=(2,), dtype=int64)
tf.Tensor([0 1], shape=(2,), dtype=int64)
tf.Tensor([2 3], shape=(2,), dtype=int64)
tf.Tensor([4 5], shape=(2,), dtype=int64)
tf.Tensor([6 7], shape=(2,), dtype=int64)
tf.Tensor([6 7], shape=(2,), dtype=int64)
tf.Tensor([8 9], shape=(2,), dtype=int64)
tf.Tensor([8 9], shape=(2,), dtype=int64)
tf.Tensor([6 7], shape=(2,), dtype=int64)
tf.Tensor([8 9], shape=(2,), dtype=int64)
tf.Tensor(0, shape=(), dtype=int64)
tf.Tensor(1, shape=(), dtype=int64)
tf.Tensor(2, shape=(), dtype=int64)
tf.Tensor(3, shape=(), dtype=int64)
tf.Tensor(4, shape=(), dtype=int64)
tf.Tensor(5, shape=(), dtype=int64)
tf.Tensor(0, shape=(), dtype=int64)
tf.Tensor(1, shape=(), dtype=int64)
tf.Tensor(2, shape=(), dtype=int64)
tf.Tensor(3, shape=(), dtype=int64)
tf.Tensor(4, shape=(), dtype=int64)
tf.Tensor(5, shape=(), dtype=int64)
tf.Tensor(0, shape=(), dtype=int64)
tf.Tensor(1, shape=(), dtype=int64)
tf.Tensor(2, shape=(), dtype=int64)
tf.Tensor(3, shape=(), dtype=int64)
tf.Tensor(4, shape=(), dtype=int64)
tf.Tensor(5, shape=(), dtype=int64)
tf.Tensor(6, shape=(), dtype=int64)
tf.Tensor(7, shape=(), dtype=int64)
tf.Tensor(8, shape=(), dtype=int64)
tf.Tensor(9, shape=(), dtype=int64)
tf.Tensor(6, shape=(), dtype=int64)
tf.Tensor(7, shape=(), dtype=int64)
tf.Tensor(8, shape=(), dtype=int64)
tf.Tensor(9, shape=(), dtype=int64)
tf.Tensor(6, shape=(), dtype=int64)
tf.Tensor(7, shape=(), dtype=int64)
tf.Tensor(8, shape=(), dtype=int64)
tf.Tensor(9, shape=(), dtype=int64)
--------------------------------------------------
<_TensorSliceDataset element_spec=(TensorSpec(shape=(2,), dtype=tf.int64, name=None), TensorSpec(shape=(), dtype=tf.string, name=None))>
tf.Tensor([1 2], shape=(2,), dtype=int64) tf.Tensor(b'cat', shape=(), dtype=string)
tf.Tensor([3 4], shape=(2,), dtype=int64) tf.Tensor(b'dog', shape=(), dtype=string)
tf.Tensor([5 6], shape=(2,), dtype=int64) tf.Tensor(b'fox', shape=(), dtype=string)
<_TensorSliceDataset element_spec={'feature1': TensorSpec(shape=(2,), dtype=tf.int64, name=None), 'label': TensorSpec(shape=(), dtype=tf.string, name=None)}>
tf.Tensor([1 2], shape=(2,), dtype=int64) tf.Tensor(b'cat', shape=(), dtype=string)
tf.Tensor([3 4], shape=(2,), dtype=int64) tf.Tensor(b'dog', shape=(), dtype=string)
tf.Tensor([5 6], shape=(2,), dtype=int64) tf.Tensor(b'fox', shape=(), dtype=string)
```
## csv文件
执行流程：

```python
!rm -rf generate_csv
```

存数据

```python
from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split
import numpy as np
import pandas as pd
import tensorflow as tf
from sklearn.preprocessing import StandardScaler
import os

# 数据准备
# -----------------------------------------------------------------------------
housing = fetch_california_housing()
x_train_all, x_test, y_train_all, y_test = train_test_split(
    housing.data, housing.target, random_state = 7)
x_train, x_valid, y_train, y_valid = train_test_split(
    x_train_all, y_train_all, random_state = 11)
print(x_train.shape, y_train.shape)
print(x_valid.shape, y_valid.shape)
print(x_test.shape, y_test.shape)
scaler = StandardScaler()
x_train_scaled = scaler.fit_transform(x_train)
x_valid_scaled = scaler.transform(x_valid)
x_test_scaled = scaler.transform(x_test)
# -----------------------------------------------------------------------------

# 下面要把特征工程后的数据存为csv文件
output_dir = "generate_csv"
if not os.path.exists(output_dir):
    os.mkdir(output_dir)

def save_to_csv(output_dir, data, name_prefix, header=None, n_parts=10):
    #生成文件名
    path_format = os.path.join(output_dir, "{}_{:02d}.csv")
    #把数据分为n_parts部分，写到文件中去 enumerate就是多一个i从0开始，不断加上去的
    for file_idx, row_indices in enumerate(np.array_split(np.arange(len(data)), n_parts)):
        #生成子文件名
        part_csv = path_format.format(name_prefix, file_idx)
        with open(part_csv, "wt", encoding="utf-8") as f:
            #先写头部
            if header is not None:
                f.write(header + "\n")
            for row_index in row_indices:
                #把字符串化后的每个字符串用逗号拼接起来
                f.write(",".join([repr(col) for col in data[row_index]]))
                f.write('\n')

#np.c_把x和y合并起来
train_data = np.c_[x_train_scaled, y_train]
valid_data = np.c_[x_valid_scaled, y_valid]
test_data = np.c_[x_test_scaled, y_test]
#头部,特征，也有目标
header_cols = housing.feature_names + ["MidianHouseValue"]
#把列表变为字符串
header_str = ",".join(header_cols)
print(header_str)
save_to_csv(output_dir, train_data, "train",header_str, n_parts=20)
save_to_csv(output_dir, valid_data, "valid",header_str, n_parts=10)
save_to_csv(output_dir, test_data, "test",header_str, n_parts=10)
```
输出：

```python
(11610, 8) (11610,)
(3870, 8) (3870,)
(5160, 8) (5160,)
MedInc,HouseAge,AveRooms,AveBedrms,Population,AveOccup,Latitude,Longitude,MidianHouseValue
```

```python
!cd generate_csv;ls
```
输出：

```python
test_00.csv  test_06.csv   train_02.csv  train_08.csv  train_14.csv  valid_00.csv  valid_06.csv
test_01.csv  test_07.csv   train_03.csv  train_09.csv  train_15.csv  valid_01.csv  valid_07.csv
test_02.csv  test_08.csv   train_04.csv  train_10.csv  train_16.csv  valid_02.csv  valid_08.csv
test_03.csv  test_09.csv   train_05.csv  train_11.csv  train_17.csv  valid_03.csv  valid_09.csv
test_04.csv  train_00.csv  train_06.csv  train_12.csv  train_18.csv  valid_04.csv
test_05.csv  train_01.csv  train_07.csv  train_13.csv  train_19.csv  valid_05.csv
```
提取数据：

```python
import pprint

# 提取数据的流程
# 获取文件名称
filenames = os.listdir('./generate_csv')
train_filenames = []
test_filenames = []
valid_filenames = []
for filename in filenames:
  if filename[0] == 'v':
    valid_filenames.append('generate_csv/'+filename)
  elif filename[1] == 'e':
    test_filenames.append('generate_csv/'+filename)
  else:
    train_filenames.append('generate_csv/'+filename)

def parse_csv_line(line, n_fields = 9):
    #先写一个默认的格式，就是9个nan,如果从csv中读取缺失数据，就会变为nan
    defs = [tf.constant(np.nan)] * n_fields
    #使用decode_csv解析
    parsed_fields = tf.io.decode_csv(line, record_defaults=defs)
    #前8个是x，最后一个是y
    x = tf.stack(parsed_fields[0:-1])
    y = tf.stack(parsed_fields[-1:])
    return x, y


def csv_reader_dataset(filenames, n_readers=5,batch_size=32, n_parse_threads=5,shuffle_buffer_size=10000):
    # 将数据集放入这个dataset当中，他会默认进行shuffle
    dataset = tf.data.Dataset.list_files(filenames)
    #变为repeat dataset可以让读到最后一个样本时，从新去读第一个样本
    dataset = dataset.repeat()
    dataset = dataset.interleave(
        #skip(1)是因为每个文件存了特征名字，target名字
        lambda filename: tf.data.TextLineDataset(filename).skip(1),
        cycle_length = n_readers
    )
    dataset.shuffle(shuffle_buffer_size) #对数据进行洗牌，混乱
    #map，通过parse_csv_line对数据集进行映射，map只会给函数传递一个参数
    dataset = dataset.map(parse_csv_line,num_parallel_calls=n_parse_threads)
    dataset = dataset.batch(batch_size)
    return dataset

# 得到dataset类
train_set = csv_reader_dataset(train_filenames, batch_size=4)


print(train_set)
#是csv_reader_dataset处理后的结果，
for x_batch, y_batch in train_set.take(2):
    print("x:")
    pprint.pprint(x_batch)
    print("y:")
    pprint.pprint(y_batch)
```
输出：

```python
<_BatchDataset element_spec=(TensorSpec(shape=(None, 8), dtype=tf.float32, name=None), TensorSpec(shape=(None, 1), dtype=tf.float32, name=None))>
x:
<tf.Tensor: shape=(4, 8), dtype=float32, numpy=
array([[-1.1199750e+00, -1.3298433e+00,  1.4190045e-01,  4.6581370e-01,
        -1.0301778e-01, -1.0744184e-01, -7.9505241e-01,  1.5304717e+00],
       [ 4.9710345e-02, -8.4924191e-01, -6.2146995e-02,  1.7878747e-01,
        -8.0253541e-01,  5.0660671e-04,  6.4664572e-01, -1.1060793e+00],
       [-6.6722274e-01, -4.8239522e-02,  3.4529406e-01,  5.3826684e-01,
         1.8521839e+00, -6.1125383e-02, -8.4170932e-01,  1.5204847e+00],
       [-3.2652634e-01,  4.3236190e-01, -9.3454592e-02, -8.4029920e-02,
         8.4600359e-01, -2.6631648e-02, -5.6176794e-01,  1.4228760e-01]],
      dtype=float32)>
y:
<tf.Tensor: shape=(4, 1), dtype=float32, numpy=
array([[0.66 ],
       [2.286],
       [1.59 ],
       [2.431]], dtype=float32)>
x:
<tf.Tensor: shape=(4, 8), dtype=float32, numpy=
array([[-1.0775077 , -0.4487407 , -0.5680568 , -0.14269263, -0.09666677,
         0.12326469, -0.31448638, -0.4818959 ],
       [-0.9490939 ,  0.6726626 ,  0.28370556,  0.1065553 , -0.65464777,
        -0.06239493,  0.21273656,  0.0024705 ],
       [-1.453851  ,  1.8741661 , -1.1315714 ,  0.36112761, -0.3978858 ,
        -0.03273859, -0.73906416,  0.64662784],
       [ 1.5180511 , -0.52884096,  0.81024706, -0.1921417 ,  0.44135395,
         0.02733506, -0.81838083,  0.8563535 ]], dtype=float32)>
y:
<tf.Tensor: shape=(4, 1), dtype=float32, numpy=
array([[0.978],
       [0.607],
       [1.875],
       [2.898]], dtype=float32)>
```


## tfrecord
Tfrecord是TensorFlow独有的数据格式，有读取速度快的优势，正常情况下我们训练文件数据集经常会生成 train, test 或者val文件夹，这些文件夹内部往往会存着成千上万的图片或文本等文件，这些文件被散列存着，这样不仅占用磁盘空间，并且再被一个个读取的时候会非常慢，繁琐。占用大量内存空间（有的大型数据不足以一次性加载）。此时我们TFRecord格式的文件存储形式会很合理的帮我们存储数据。TFRecord内部使用了“Protocol Buffer”二进制数据编码方案，它只占用一个内存块，只需要一次性加载一个二进制文件的方式即可，简单，快速，尤其对大型训练数据很友好。而且当我们的训练数据量比较大的时候，可以将数据分成多个TFRecord文件，来提高处理效率。

之后补，这边采用csv也可以，方法类似，这边先跳，之后补充。
