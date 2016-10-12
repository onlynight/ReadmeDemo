AIDL使用以及原理分析
==============

AIDL的使用
-----------
注：由于基于eclipse的adt过于老旧这里不再讲解操作，请使用android studio完成以下操作。

## **·** 使用AIDL文件

###**1.新建aidl文件**
在你想要创建aidl的包下新建aidl文件（这里我们命名为IDataManager），aidl文件的语法与java类似，默认生成的aidl会有一个demo方法

```
void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,double aDouble, String aString);
```
<br>
```basicTypes```这个是系统生成的方法，告诉我们能够传递那些类型的数据。
###**2.添加自定义方法**

```
import org.github.lion.aidl_demo.Data;
// Declare any non-default types here with import statements

interface IDataManager {
	/**
	 * Demonstrates some basic types that you can use as parameters
	 * and return values in AIDL.
	 */
	void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,double aDouble, String aString);

	int getDataTypeCount();

	List<Data> getData();

	String getUrlContent(String url);
}
```
```List<Data> getData()```这个方法中使用了自定义的数据类型，虽然我们在文件开头写了import但是还是无法通过编译，我们需要在sdk的platform下修改framework.aidl，完整路径如下:```~/platforms/android-xx/framework.aidl```，加入我们自己添加的类名即可：
```
// user define aidl parcelable data
parcelable org.github.lion.aidl_demo.Data;
```
**```Data```需实现```Parcelable```接口。**
以下是android studio的默认实现。
```
/**
 * Created by lion on 2016/10/11.
 * 要通过Bundle传递的数据需要实现Parcelable接口，
 * 一旦你实现了这个接口android studio会提示你帮
 * 你快速实现带有Parcel的构造函数。
 */
public class Data implements Parcelable {

	...

	protected Data(Parcel in) {
		...
	}

	public static final Creator<Data> CREATOR = new Creator<Data>() {
		@Override
		public Data createFromParcel(Parcel in) {
			return new Data(in);
		}

		@Override
		public Data[] newArray(int size) {
			return new Data[size];
		}
	};
}
```

###**3. 编译aidl文件**
到这里aidl的编写就完成了，我们build下工程，编译器会自动生成IDataManager.java文件。
该文件在工程的```~/app/build/generated/source/aidl/debug/<package>/IDataManager.java```，这里我们先不讲解生成的这个类，先看下如何使用aidl。
	
###**4. 添加Service类（远端服务）**
添加一个```Service```命名为```DataManagerService```我们在```DataManagerService```中实现一个静态的```IDataManager.Stub```的类

```
private static final IDataManager.Stub mBinder = new IDataManager.Stub() {

	@Override
	public void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat, double aDouble, String aString) throws RemoteException {
	}

	@Override
	public int getDataTypeCount() throws RemoteException {
		// todo return some data
		return 0;
	}

	@Override
	public List<Data> getData() throws RemoteException {
		// todo return some data
		return null;
	}

	@Override
	public String getUrlContent(String url) throws RemoteException {
		// todo return some data
		return null;
	}
};
```

## **·**自己实现Binder

AIDL原理分析
-------------