# ；python 问题数字

> 原文：<https://dev.to/elicodingpython/python-problem-numpy-5bc3>

# 第一部分这里是问题……...

# 多元多步一维 cnn 示例

from numpy 导入数组
from numpy 导入 hstack
from keras.models 导入顺序
from keras.layers 导入密集
from keras.layers 导入扁平化
from keras.layers .卷积导入 Conv1D
from keras.layers .卷积导入 MaxPooling1D

# 将一个多元序列拆分成样本

def split_sequences(sequences，n_steps_in，n_steps_out):
X，y = list()，list()
for I in range(len(sequences)):
#找到此模式的结尾
end _ IX = I+n _ steps _ in
out _ end _ IX = end _ IX+n _ steps _ out-1
#检查我们是否超出数据集
if out _ end _ IX>len(sequences):
break
#收集输入

# 定义输入顺序

in_seq1 = array([10，20，30，40，50，60，70，80，90])
in_seq2 = array([15，25，35，45，55，65，75，85，95])
out _ seq = array([in _ seq 1[I]+in _ seq 2[I]for I in range(len(in _ seq 1))))

# 转换为【行，列】结构

in _ se Q1 = in _ se Q1 . shape((len(in _ se Q1)，1))
in _ seq 2 = in _ seq 2 . shape((len(in _ seq 2)，1))
out _ seq = out _ seq . shape((len(out _ seq)，1))

# 水平堆叠列

dataset = hstack((in_seq1，in_seq2，out_seq))

# 选择若干时间步

n_steps_in，n_steps_out = 3，2

# 转换成输入/输出

x，y = split_sequences(数据集，n_steps_in，n_steps_out)

# 数据集知道特征的数量，例如 2

n_features = X.shape[2]

# 定义模型

model = Sequential()
model . add(Conv1D(filters = 64，kernel_size=2，activation='relu '，input_shape=(n_steps_in，n _ features)))
model . add(MaxPooling1D(pool _ size = 2))
model . add(Flatten())
model . add(Dense(50，activation = ' relu ')
model . add(Dense(n _ steps _ out))
model . compile(optimizer = ' Adam '，loss='mse

# 拟合模型

model.fit(X，y，epochs=2000，verbose=0)

# 演示预测

x_input = array([[70，75]，[80，85]，[90，95]])
x _ input = x _ input . shape((1，n_steps_in，n _ features))
yhat = model . predict(x _ input，verbose=0)
print(yhat)