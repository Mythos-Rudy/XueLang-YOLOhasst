


# 前言
本项目由锐捷网络智慧教室郑智雄、吴宏和、黄杰三人共同完成；


# 视觉计算辅助良品检测

- 参赛地址：[雪浪制造AI挑战赛](https://tianchi.aliyun.com/competition/introduction.htm?spm=a2c22.11695015.1131732.1.4ea25275NNvZuf&raceId=231666) 


----------

# YOLOhasst
  - ##  You Only Look One hundred and sixty-six times

YOLOhasst是一种'快速'的瑕疵检测方法，它使用了全局检测和局部检测两个模型进行融合，对于一张完整的布匹图像，'仅仅'需要检测166次就能得出结果。因此，我们给它取了个名字 You Only Look One hundred and sixty-six times....

----------
- 1.在比赛的开始，由于硬件条件的限制（一块1080ti，11G），我们首先想到的方法就是对完整的大图进行切割，然后分别预测并在最终整合结果。这个方法再加上滑动检测、最大值抑制等一些小技巧，达到了0.932的成绩，一度排到了第一名。

```
切割方法：
训练阶段我们将整张大图切成6*8份，每份320*320的大小。我们选择瑕疵面积占完整瑕疵面积的比例大于0.09的小图作为瑕疵样本；完全没包含瑕疵的小图作为正常样本。

模型：
我们尝试了resnet50\VGG16\Inception-ResNet-v2等模型，经过实验效果相差不大，最终我们选择的是Inception-ResNet-v2的模型，并在最后加上了一层SPP层用来识别更细小的瑕疵。

滑动检测：
预测时我们并不是把全图切为6*8份进行预测，而是使用320*320的大小，160的步长进行滑动预测，总共需要预测11*15张小图；（成绩从0.9提升到0.92）

最大值抑制：
整合结果时我们没有直接使用最大概率值，而是取前三大的概率进行了平均。（成绩从0.92提升到0.932）
```


- 2.随后方法1的切割遇到了瓶颈，我们发现该方法容易将布匹边缘误检测为瑕疵，在尝试了不同的切割和模型后都无法突破0.932。这时候我们想到了YOLO论文中的第一句话‘Humans glance at an image and instantly know what objects are in the image, where they are, and how they interact’。然后我们开始很哲学地去思考，对于布匹瑕疵检测这个问题，如果是人眼来找瑕疵会怎么做。应该是有两步，一是一眼看过去有没有瑕疵，然后再一块块细看有没有小的瑕疵。实际上我们方法1的切割对应的就是第二步，而我们还缺了第一步，就是全局地去查看。于是，我们就使用了一个土办法，将全图resize成800*600（主要还是由于硬件限制）后单独训练了一个模型，再和方法一的模型进行融合。全图的模型单独的分数可以达到0.914。

----------

- 3.将方法一和方法二进行简单地融合后（结果平均），成绩达到了0.952，更换成testb以后达到了0.953.
最终我们计算了一下，方法一加上滑动检测后需要对一张大图检测11*15=165次，方法二检测1次，总共检测了165+1=166次。这就是我们最终笨拙但还算有效的YOLOhasst的方法，You Only Look One hundred and sixty-six times....

----------



# 使用步骤：

1.首先将初赛官方的测试图片解压放到data\official文件夹中。

2.将初赛的2次测试图片和公布的答案解压放在test\testa和test\testb文件夹中。

3.打开main.ipynb，依次运行每一个cell即可，程序会自动保存模型，最后会计算testa和testb中的auc


# 注意事项：

1.程序运行过程中会生成训练所需的cut和resize图片并保存在data目录下的对应文件夹中，若第一次运行后已生成图片，第二次运行可跳过cell2-4，直接从Train Model A开始；或者删除生成的中间文件夹，仅保留data\official文件夹，然后重头运行，避免重复保存中间图片。

# 比赛数据下载：

链接：https://pan.baidu.com/s/1Mi2brFATDDyPOpsBFUKgJQ 

密码：1z3p

### PS：有任何问题，可以联系我们（QQ:995187509 邮箱：995187509@qq.com)
