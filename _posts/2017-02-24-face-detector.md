---
layout:     post
title:      "Face Detector 臉部辨識(CIDetector)"
date:       2017-02-24
author:     "John Wu"
catalog:    true
tags:
    - iOS
    - Google
    - FaceDetector
    - Camera
---

2月初在研究臉部辨識的framework，很廢的我現在才發現有原生的接下來來介紹如何使用基本的CIDetector。

### CIDetector介紹
 
[CIDetector](https://developer.apple.com/reference/coreimage/cidetector) 先給你看一下他的官方文件，CIDetector前綴字是CI所以很明顯的他是吃Core Image，跟我們平常使用的UIImage有點不同，稍後會介紹。

官方說明

>A CIDetector object uses image processing to search for and identify notable features (faces, rectangles, and barcodes) in a still image or video. Detected features are represented by CIFeature objects that provide more information about each feature.
This class can maintain many state variables that can impact performance. So for best performance, reuse CIDetector instances instead of creating new ones.

### 介紹UIKit與CoreImage
- Core Image和UIKit使用的是2種不同的座標系統請見下圖(截自網路）。所以稍後我們必須轉換兩者座標系統。

<img src="/img/post/2017-02-24-face-detector/UIKitCoreImage.png" style="width: 400px;"/>

### 開始
這篇教學我透過手機攝像頭擷取影像，並且即時抓出你臉部位置，如果只是要偵測圖像的話也可以參考我這邊，省掉攝像頭那部分。
<br>
#### 初始化CIDetector
```
private func initialDetector(){
    let accuracy = [CIDetectorAccuracy : CIDetectorAccuracyHigh as AnyObject]
    faceDetector = CIDetector(ofType: CIDetectorTypeFace, context: nil, options: accuracy)
}
```
#### 設置Session Input
```
   private func setupSession(){
        let desiredPosition = isFrontCam! ? AVCaptureDevicePosition.front : AVCaptureDevicePosition.back
        let device = self.cameraPosition(position: desiredPosition)
        var deviceInput: AVCaptureDeviceInput?
        do{
            deviceInput = try AVCaptureDeviceInput(device: device)
        }catch{
            print(error)
        }
        session.sessionPreset = AVCaptureSessionPresetHigh
        session.addInput(deviceInput)
    }
```
#### 設置Camera Preview
```
    private func setupCameraPreview(){
        previewLayer = AVCaptureVideoPreviewLayer(session: session)
        previewLayer?.frame = captureImage.bounds
        previewLayer?.videoGravity = AVLayerVideoGravityResizeAspectFill
        captureImage.layer.addSublayer(previewLayer!)
    }
```
#### 設置Session Output
```
    private func setupSessionOutput(){
        self.videoOutput = AVCaptureVideoDataOutput()
        self.videoOutput!.videoSettings = [kCVPixelBufferPixelFormatTypeKey as AnyHashable: Int(kCVPixelFormatType_32BGRA)]
        self.videoOutput?.alwaysDiscardsLateVideoFrames = true
        self.videoOutputQueue = DispatchQueue(label: "videoOutputQueue")
        self.videoOutput?.setSampleBufferDelegate(self, queue: self.videoOutputQueue)
        session.addOutput(videoOutput)
        session.startRunning()
    }
```
### 截取Output
基本設置完後，要開始擷取攝像頭的Output，調用captureOutput函式要拿到CMSampleBuffer媒體數據轉換成我們需要的圖檔

#### 接取Session Output

```
    func captureOutput(_ captureOutput: AVCaptureOutput!, didOutputSampleBuffer sampleBuffer: CMSampleBuffer!, from connection: AVCaptureConnection!) {
        autoreleasepool {
            connection.videoOrientation = AVCaptureVideoOrientation.portrait
            connection.isVideoMirrored = isFrontCam!
            let imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer)
            let opaqueBuffer = Unmanaged<CVImageBuffer>.passUnretained(imageBuffer!).toOpaque()
            let pixelBuffer = Unmanaged<CVPixelBuffer>.fromOpaque(opaqueBuffer).takeUnretainedValue()
            let sourceImage = CIImage(cvImageBuffer: pixelBuffer, options: nil)
            let options = [CIDetectorSmile : true as AnyObject, CIDetectorEyeBlink: true as AnyObject, CIDetectorImageOrientation : 1 as AnyObject]
            let faces = faceDetector?.features(in: sourceImage, options: options) as? [CIFaceFeature]
            if faces?.count != 0 {
                let viewSize = captureImage.bounds.size
                let ciImageSize = sourceImage.extent.size
     			let scaleX = viewSize.width / ciImageSize.width
                let scaleY = viewSize.height / ciImageSize.height
                let offsetX = (viewSize.width - ciImageSize.width * scaleX) / 2
                let offsetY = (viewSize.height - ciImageSize.height * scaleY) / 2
                for face in faces!{
                    DispatchQueue.main.sync {
                        var facebound = face.bounds.applying(coreImage2UIviewTransform(ciImage: sourceImage))
                        facebound = facebound.applying(CGAffineTransform(scaleX: scaleX, y: scaleY))
                        facebound.origin.x += offsetX
                        facebound.origin.y += offsetY
                        faceBound?.faceLocal = facebound
                    }
                }
            }
        }
    }
```
[Feature Detection Option](https://developer.apple.com/reference/coreimage/cidetector/feature_detection_keys)官網有option的解說
- CIDetectorImageOrientation為1代表portrait
- ciImageSize為攝像頭拍攝出的圖像size，所以sessionPreset設為多少這邊size就為多少
- 接著我們要計算圖片與螢幕的縮放大小，所以是viewSize / ciImageSize （前者為螢幕大小，後者為圖片大小）
- 得到face的bounds後，實作coreImage轉換至UIKit的座標並且縮放face的bounds

再看一次
<img src="/img/post/2017-02-24-face-detector/UIKitCoreImage.png" style="width: 400px;"/>

#### 轉換座標的函式
```
    private func coreImage2UIviewTransform(ciImage: CIImage) -> CGAffineTransform{
        let ciImagesize = ciImage.extent.size
        var transform = CGAffineTransform(scaleX: 1, y: -1)
        transform = transform.translatedBy(x: 0, y: -ciImagesize.height)
        return transform
    }
```

### 完成
[CIFaceFeature](https://developer.apple.com/reference/coreimage/cifacefeature)官方文件
這邊有相關的資料，可以抓取眼睛耳朵嘴巴諸如此類的，然後試用後感覺有些慢，沒像原生的照相機這麼快，不知道是不是我寫的問題
，下一篇會介紹GoogleMobileVision的臉部辨識，功能更強大了些。






