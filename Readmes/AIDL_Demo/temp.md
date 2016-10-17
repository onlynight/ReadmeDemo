```java
public interface IDataManager extends android.os.IInterface {

    /**
     * Local-side IPC implementation stub class.
     */
    public static abstract class Stub extends android.os.Binder implements org.github.lion.aidl_demo.IDataManager {

    	private static class Proxy implements org.github.lion.aidl_demo.IDataManager {}
    }
}
```