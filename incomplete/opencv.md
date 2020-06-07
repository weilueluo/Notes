# [OpenCV Notes](https://docs.opencv.org/)

## Mat - Basic Image Container

In the old days (OpenCV 1+), the library was built using C and image is represented using a struct called [**IplImage**](https://docs.opencv.org/3.4.8/d6/d5b/structIplImage.html). The biggest issue with it is that user has to handle memory manually. But luckily C++ came along with the concept of classes which make user easier through automatic memory management. Thus OpenCV 2.0 introduce a C++ interface so that user do not need to fiddle with memory management.

### Matrix Referencing

**Mat** is a class containing two parts, the matrix header (containing information about the image, constant size) and a pointer to the matrix; while each Mat has its own header, two Mat can referencing to the same matrix. This is because speed is very important for image processing library, it wants to pass around the image without making unnecessary copies. Internally it has a reference count to keep track of number of Mat referencing the matrix.

```c++
// all Mat below is referencing to the same matrix.
Mat a = imread("path/to/image");
Mat b(a);
Mat c = b;

Mat d(a, Rect(100, 100, 200, 200)); // a rectangle region of the same matrix
Mat e(a, Range()::all(), Range(1, 3));  // using rows and columns boundaries
```

```c++
// Mat here are referencing to different matrix
Mat a = imread("path/to/image");
Mat b = a.clone();
Mat c;
a.copyTo(c);
```

### Explicit Creation

- **cv::Mat::Mat** Constructor

  ```c++
  // 2D matrix
  // CV_8UC3 is the datatype which defined as:
  // CV_[num bits][signed or not][Type prefix]C[num channels]
  Mat a(2, 2, CV_8UC3, Scalar(0, 0, 255));
  // [[0,0,255, 0,0,255],
  //  [0,0,255, 0,0,255],
  //  [0,0,255, 0,0,255]]
  
  // 3D matrix
  int sz[3] = {2,2,2};
  Mat L(3,sz, CV_8UC(1), Scalar::all(0));
  // Note that OpenCV cannot print matrix with dimension >= 3
  // i.e. cout << L << endl; will throw an exception
  ```
  
- MATLAB style

  ```c++
  Mat E = Mat::eye(4, 4, CV_64F);
  cout << "E = " << endl << " " << E << endl << endl;
  Mat O = Mat::ones(2, 2, CV_32F);
  cout << "O = " << endl << " " << O << endl << endl;
  Mat Z = Mat::zeros(3,3, CV_8UC1);
  cout << "Z = " << endl << " " << Z << endl << endl;
  ```

- **cv::Mat::create**

  ```c++
  // Note that we cannot initialize values with this style
  M.create(4,4, CV_8UC(2));
  cout << "M = "<< endl << " "  << M << endl << endl;
  ```

- Smaller matrix

  ```c++
  C = (Mat_<double>({0, -1, 0, -1, 5, -1, 0, -1, 0})).reshape(3);
  cout << "C = " << endl << " " << C << endl << endl;
  ```

### Output Formatting

```c++
cout << M << endl;  // default
cout << format(M, Formatter::FMT_CSV   ) << endl;
cout << format(M, Formatter::FMT_NUMPY ) << endl;
cout << format(M, Formatter::FMT_NUMPY ) << endl;
```



### Storing Methods

- **RGB**, most common as our eyes uses something similar, but note that OpenCV uses **BGR** due to [historically reason](https://stackoverflow.com/questions/14556545/why-opencv-using-bgr-colour-space-instead-of-rgb).
- **HSV/HLS**, hue, saturation and value/luminance. More naturally way of describing color and useful for example when you want your algorithm less sensitive to light by omitting the last component.
- **YCrCb**, used by JEPG images.
- **CIE l\*a\*b\***, perceptually uniform color space that comes in handy when you want to measure distance between two colors.

### Look-Up Table

```
Mat& lookupTable(Mat& mat, const uchar* const table) {
	// accept only char type matrix
	CV_Assert(mat.depth() == CV_8U);
	
	int nRows = mat.rows;
	int nCols = mat.cols * mat.channels;
	if (mat.isContinuous()) {
		nCols *= nRows;
		nRows = 1;
	}
	
	for (int row = 0; row < nRows; row++) {
		uchar* ptr = mat.ptr<uchar>(row);
		for (int col = 0; col < nCols; col++) {
			ptr[col] = table[ptr[col]];
		}
	}
	return mat;
}
```



