---
title: CV2的常用方法
categories:
    - Python
tags:
    - Python
    - 图像处理
---

#### 1. 图片读取显示

```python
img = cv.imread("1.jpg")  # 图像读取
x,y = img.shape[:2] #获取图片的高和宽
# 创建一个窗口
cv.namedWindow('test',cv.WINDOW_NORMAL) # 通过cv.WINDOW_NORMAL可以完整显示图片
cv.imshow('test',img) # 显示图片
cv.waitKey(0) # 等待键盘输入，0表示无限等待，如果不调用waitKey窗口就会一闪而过，看不到任何图片
cv.destroyAllWindows() # 销毁所有串口
```

#### 2. 图片的操作-修改图片大小

通过cv2.resize可以修改图片的大小，指定尺寸size（宽，高）,并且可以指定插入方式，不同插入方式得到的缩放图片的效果不一样。

```python
img = cv.imread("1.jpg")
x, y = img.shape[:2]  # 获取图片的高和宽
# 输出尺寸为(宽，高)
"""
cv2.resize(InputArray src, OutputArray dst, Size, fx, fy, interpolation)
InputArray src： 输入图片
InputArray src:  输出图片
Size: 输出图片尺寸(宽，高)
fx,fy:沿x轴，y轴的缩放系数
interpolation:插入方式
插入方式有如下几种：
INTER_NEAREST：最近邻插值
INTER_LINEAR：双线性插值（默认设置）
INTER_AREA:使用像素区域关系进行重采样。
INTER_CUBIC:4x4像素邻域的双三次插值
INTER_LANCZOS4:8x8像素邻域的Lanczos插值
"""
img1 = cv.resize(img,(int(y/4),int(x/4))) # 修改图片大小，缩放为原来的1/4
#cv.namedWindow('test',cv.WINDOW_NORMAL)
cv.imshow('test',img1)
cv.waitKey(0)
cv.destroyAllWindows()
img2 = cv.resize(img,(0,0),fx=0.25,fy=0.25,interpolation=cv.INTER_AREA)
cv.imshow('test', img2)
cv.waitKey(0)
cv.destroyAllWindows()

```

#### 3. 图片的操作-灰度化

```python
    img = cv.imread("1.jpg")
    img_gray = cv.cvtColor(img,cv.COLOR_BGR2GRAY) # 灰度化
    cv.namedWindow('test', cv.WINDOW_NORMAL)
    cv.imshow('test', img_gray)
    cv.waitKey(0)
    cv.destroyAllWindows()
```


#### 4.OpenCV和PIL图像之间的转换

以下代码包含了将cv图片转换为pil图片，然后将Pil图片转换为cv图片。以及如何给图片增加文字信息。
```python
pilimg = Image.fromarray(cv2.cvtColor(img,cv2.COLOR_BGR2RGB)) # 将cv图片转化为pil图片
draw = ImageDraw.Draw(pilimg)   
font = ImageFont.truetype("C:\Windows\Fonts\FZSTK.TTF",25,encoding='utf-8')
draw.text(((d.left()+d.right())/2,d.top()+10),"我",(255,0,0),font=font)
img = cv2.cvtColor(np.array(pilimg),cv2.COLOR_RGB2BGR) #将pil图片转换为cv图片
```