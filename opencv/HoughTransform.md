# 霍夫线变换(1)

## 标准霍夫变换(1)

```cpp
#include "pch.h"
#include <opencv2/opencv.hpp>
#include <opencv2/imgproc/imgproc.hpp>
using namespace std;
using namespace cv;

int main() {
	/*[1]载入原始图和Mat变量定义*/
	Mat srcImage = imread("1.jpg");
	Mat midImage, dstImage;

	/*[2]进行边缘检测和转化为灰度图*/
	Canny(srcImage, midImage, 50, 200, 3); //边缘检测后图为灰度图
	cvtColor(midImage, dstImage, CV_GRAY2BGR);

	/*[3]进行霍夫线变换*/
	vector<Vec2f> lines;
	HoughLines(midImage, lines, 1, CV_PI / 180, 50, 0, 0);

	/*[4]依次在图中绘制出每条线段*/
	for (size_t i = 0; i < lines.size(); ++i) {
		float rho = lines[i][0], theta = lines[i][1];
		Point pt1, pt2;
		double a = cos(theta), b = sin(theta);
		double x0 = a * rho, y0 = b * rho;
		pt1.x = cvRound(x0 + 1000 * (-b));
		pt1.y = cvRound(y0 + 1000 * a);
		pt2.x = cvRound(x0 - 1000 * (-b));
		pt2.y = cvRound(y0 - 1000 * a);
		line(dstImage, pt1, pt2, Scalar(55, 100, 195), 1, LINE_AA);
	}

	/*[5]显示*/
	imshow("原图", srcImage);
	imshow("边缘检测后的图", midImage);
	imshow("线图", dstImage);

	waitKey(100000);

	return 0;
}
```

![image](https://github.com/zhouhuahui/image/blob/master/tech_notes/opencv/HoughTransform/1.1.1.jpg?raw=true)

## 累计概率霍夫变换(2)

```cpp
//核心代码改为以下

/*[3]进行霍夫线变换*/
	vector<Vec4i> lines;
	/*
	*参数5：表示阈值；参数6：表示最低线段的长度；参数7：同一行点与点连接的最大长度
	*/
	HoughLinesP(midImage, lines, 1, CV_PI / 180, 50, 50, 10);

	/*[4]依次在图中绘制出每条线段*/
	for (size_t i = 0; i < lines.size(); ++i) {
		Vec4i l = lines[i];
		line(dstImage, Point(l[0], l[1]), Point(l[2], l[3]), Scalar(186, 88, 255), 1, LINE_AA);
	}
```

![image](https://github.com/zhouhuahui/image/blob/master/tech_notes/opencv/HoughTransform/1.2.1.jpg?raw=true)

显然，我觉得HoughLinesP得效果会更好

# 霍夫圆变换(2)

```cpp
#include "pch.h"
#include <opencv2/opencv.hpp>
#include <opencv2/imgproc/imgproc.hpp>
using namespace std;
using namespace cv;

int main() {
	/*[1]载入原始图和Mat变量定义*/
	Mat srcImage = imread("1.jpg");
	Mat midImage;

	imshow("原图", srcImage);

	/*[2]转化为灰度图并进行图像平滑*/
	Canny(srcImage, midImage, 50, 200, 3);
	GaussianBlur(midImage, midImage, Size(9, 9), 2, 2);

	/*[3]进行霍夫圆变换*/
	vector<Vec3f> circles;
	HoughCircles(midImage, circles, HOUGH_GRADIENT, 1.5, 10, 200, 50, 0, 0);

	/*[4]依次在图中绘制出每条线段*/
	for (size_t i = 0; i < circles.size(); ++i) {
		Point center(cvRound(circles[i][0]), cvRound(circles[i][1]));
		int radius = cvRound(circles[i][2]);
		//绘制圆心
		circle(srcImage, center, 3, Scalar(0,255,0), -1, 8, 0);
		//绘制圆轮廓
		//circle(srcImage, center, radius, Scalar(155, 50, 255), 3, 8, 0);
	}

	/*[5]显示*/
	imshow("检测的圆图", srcImage);

	waitKey(100000);

	return 0;
}
```

![image](https://github.com/zhouhuahui/image/blob/master/tech_notes/opencv/HoughTransform/2.1.jpg?raw=true)

这是我从一个不好的样本测试的霍夫圆变换得结果，在``HoughCircles``的第7个参数（para7, 越小越可以检测到不像圆的东西）为50时可以检测到硬币的圆心，而且50是可以检测到硬币的最大的para7，**但也要相应的做一些排除操作，排除那些肯定这不是的**，比如通过硬币的RGB的大概的值的范围来选择哪个点最合适（这是我的粗糙思路）。