# 无人机APP

文档:

https://developer.dji.com/cn/api-reference-v5/android-api/Components/SDKManager/DJISDKManager.html

`CameraKey`提供了一组方法来设置和获取相机参数。包括相机类型，拍照录像，镜头设置，红外设置等功能。

```java
 class CameraKey extends DJICameraKey
```

### 视频录制至SD卡

##### KeyStartRecord

开始录制视频。需要先调用`KeyCameraMode`把相机工作模式设置为`VIDEO_NORMAL`。对于红外镜头，用户可以在录制视频时拍摄照片。使用此方法前应检查SD卡状态，以确保有足够的空间。

`RemoteControllerKey`提供了一组方法来设置和获取遥控器数据。遥控器是一种具有摇杆、按钮、滚轮、GPS、电池、和视频输出端口的设备。移动设备可以连接到遥控器，遥控器会向移动设备发送任何从飞行器上返回的信息。

```java
class RemoteControllerKey extends DJIRemoteControllerKey
```



### 变焦

###### final KeyCameraZoomRatios

```
static final DJIKeyInfo<Double> KeyCameraZoomRatios = new KeyCameraZoomRatios()
           .canGet(true).canSet(true).canListen(true).canPerformAction(false).setIsEvent(false)
```

##### 描述：

**参数:**Double
设置相机镜头变焦倍率。建议设置的最小精度为0.1。

**注意：**

1. 要使用此功能，请调用`KeyCameraVideoStreamSource`把视频源设置为`ZOOM_CAMERA`。
2. 使用`KeyTools`创建`DJIKey`实例的时候，把`CameraLensType`设置为`CAMERA_LENS_ZOOM`。



### 对焦

###### final KeyCameraFocusTarget

```
static final DJIKeyInfo<DoublePoint2D> KeyCameraFocusTarget = new DJIKeyInfo<>(componentType.value(),subComponentType.value(),"CameraFocusTarget", new DJIValueConverter<>(DoublePoint2D.class)).canGet(true).canSet(true).canListen(true).canPerformAction(false).setIsEvent(false)
```

##### 描述：

**参数:**`DoublePoint2D`
相机自动对焦的对焦目标。[0,0]代表相机屏幕的左上角，[1,1]代表相机屏幕的右下角。在`AF`模式下，设置对焦目标后，相机将自动以对焦目标为中心点进行一次对焦。

**注意：**

1. 要使用此功能，请调用`KeyCameraVideoStreamSource`把视频源设置为`ZOOM_CAMERA`。
2. 使用`KeyTools`创建`DJIKey`实例的时候，把`CameraLensType`设置为`CAMERA_LENS_ZOOM`。





```
<TextView
    android:id="@+id/cameraRecordStatus"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="15dp"
    android:layout_marginTop="15dp"
    android:text="录像关闭"
    android:textColor="@color/green"
    app:layout_constraintStart_toEndOf="@+id/megaphoneStatus"
    app:layout_constraintTop_toTopOf="parent" />
```