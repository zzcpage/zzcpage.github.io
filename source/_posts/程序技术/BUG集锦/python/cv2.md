---
title: CV2问题集锦
categories:
    -  Bug
tags:
    - Bug
---

#### 1.  关于cv2.imshow不显示，一闪而过的问题

我们在使用cv2显示图片的时候，一定要加上cv2.waitKey进行等待图片，否则会不显示。示例代码如下

```python
    img = cv.imread("1.jpg")  # 图像读取
    x,y = img.shape[:2] #获取图片的高和宽
    # 创建一个窗口
    cv.namedWindow('test',cv.WINDOW_NORMAL) # 通过cv.WINDOW_NORMAL可以完整显示图片
    cv.imshow('test',img) # 显示图片
    cv.waitKey(0) # 等待键盘输入，0表示无限等待，如果不调用waitKey窗口就会一闪而过，看不到任何图片
    cv.destroyAllWindows() # 销毁所有串口
```