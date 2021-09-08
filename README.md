# 调研游览器对 MSE(Media Source Extensions) 兼容情况
## 结论:
| Codec\Browser      | Chrome | Chromium | Safari(Intel) | Safari(M1) | Edge | Edge(Old) | Firefox | IE |
| ------------------ | ------ | -------- | ------------- | ---------- | ---- | --------- | ------- | -- |
| FMP4/H.265/hev1    | No     | No       | Yes           | Yes        | No   | Yes       | No      | No |
| FMP4/H.265/hvc1    | No     | No       | No            | No         | No   | No        | No      | No |
| FMP4/H.264/avc1    | Yes    | Yes      | Yes           | Yes        | Yes  | Yes       | No      | Yes |
| Webm/VP8           | Yes    | Yes      | No            | No         | Yes  | Yes       | Yes     | No |
| Webm/VP9           | Yes    | Yes      | No            | No         | Yes  | Yes       | No      | No |

## 测试硬件:
- MacBook Pro 19年 i7-9750H
- MacBook Pro 15年 i5-5257U
- MacBook Pro 20年 Apple M1
- Asus Vivobook 21年 R7-5800H
- Asus Notebook i5-7200U / GT-940MX
- Lenovo 暗夜精灵 i5-7300HQ / GTX-1050Ti

## 测试环境:
- 系统 Windows 10 1803 / Windows 10 21H1 / MacOS Catalina / MacOS Big Sur
- 游览器版本 Chrome92+ / Chromium92+(all-codec+) / Safari(Intel) / Safari(M1) / Edge92+ / Edge(Old)42(EdgeHTML 17.17134) / Firefoxf91 / IE11
- 编码环境 Windows10 均未安装HEVC 所以暂时无法验证CPU硬解 原因: 收费
![image](https://user-images.githubusercontent.com/12267648/131796896-e1745072-34e3-4c8b-a50d-62699a7b69a5.png)
- Windows 采用显卡硬解 MacOS 采用CPU硬解

## 测试用例:
1. 监控摄像头拍摄 MP4/H.265/hvc1 的视频 5分钟 (无音频)
2. 标准 MP4/H.264/avc1 视频 24 分钟

## 测试方法:
1. 将视频1分别转码为FMP4(H.265/hev1 H.265/hvc1 H.264/avc1) Webm(VP8 VP9) 使用MSE播放指定编解码播放
2. 将视频2分别转码为FMP4(H.265/hev1 H.264/avc1) Webm(VP8 VP9) 使用MSE播放指定编解码播放
3. MSE设置的编解码有:
```
video/mp4; codecs="hvc1" 
video/mp4; codecs="hev1"
video/mp4; codecs="avc1.42E01E, mp4a.40.2"
video/webm; codecs="vp8"
video/webm; codecs="vp9"
 ```
 
## 测试中遇到的错误:
1. DOMException: The quota has been exceeded. 
 > Firefox报错无法播放 FMP4/H.264/avc1 视频
2. DOMException: Failed to execute 'endOfStream' on 'MediaSource': The MediaSource's readyState is not 'open'
 > 视频不是FMP4/Webm
 >> 无法支持视频本身的编解码
 >>> 无法播放没有音频流的 FMP4/H.264/avc1 视频
3. Safari(Intel) Edge(Old) 播放 FMP4/H.265/hvc1 码流时, 只能部分解码显示1/3画面. 
4. Safari(M1) 播放 FMP4/H.265/hvc1 码流时, 只能加载视频时长画面都无法显示.
5. FMP4和Webm在所有游览器下都无法直接分片加载播放, 预计是需要自行编码加载播放.

## 其他结论:
1. Windows在硬件支持的情况下使用Edge(Old)可以使用实现H.265/hev1流畅播放
2. MSE本身游览器支持度很高并且是硬件解码, 所以对硬件设备有要求:
![image](https://user-images.githubusercontent.com/12267648/131797013-c83be538-bfb4-4825-9636-3f288612a7da.png)
3. CPU(Intel Core 7代及其以上/AMD 不详) | GPU [这里](https://developer.nvidia.com/video-encode-and-decode-gpu-support-matrix-new)
4. MSE支持的Box格式为FMP4和Webm
5. MSE受限游览器提供的编解码库(对 FMP4/H.265/hvc1 支持都很差)
6. 在Safari游览器下, MSE 对 FMP4/H.265/hev1 不受限硬件(5代i5和9代i7均可流畅播放)
7. MSE 测试到视频1在没有音频流的情况下 FMP4/H.264/avc1 也无法播放
8. H.265/hev1 不是免费的(Apple Support), H.265/hvc1 是开源免费的(压缩比,普及率较 MP4/H.265/hev1 低)
9. Chromium 后面预计普及的最优编解码为AV1(libaom-av1) 这是开源免费的 [这里](https://chromium.woolyss.com/#html5-audio-video)

## 其他H.265播放器方案:
1. 1.WebAssembly + Canvas 播放器: 使用ffmpeg.js软解, 卡顿明显, 解码性能差, 技术门槛高, 兼容性很好. (能正常播放MP4/H.265/hvc1视频)
2. 视频转码到 VP9(libvpx-vp9) 压缩率更高/画质不变/Chrome兼容度高/开源免费.
3. 视频转码到 H.264/avc1(libx264) : 转码消耗方面视频码率越大转码速度越慢, 可考虑降低视频码率/分辨率/提升CRF等方式进行转码, 既能保证画质也能保证转码效率.
4. 游览器直接软解: Chromium92+(all-codec+) 集成了h.265解码器, 使用iframe嵌套方式可以直接播放, 支持hvc1和hev1

## 其他方案举例:

### H.264 转码方案举例:
```
# 1080P to 720P / hvc1 to avc1 / yuvj420p to yuv420p / bitrate 2164k to 311k / CPU R7-5800H 8C16T / Speed 12.5x
ffmpeg.exe -i .\test.mp4 -threads 16 -preset ultrafast -vf scale=1280:720 -b:v 300k -c:v libx264 -pix_fmt yuv420p -hide_banner test-720-avc-300k-yuv420p.mp4
```

### 使用iframe嵌套播放(Chromium92+(all-codec+))
```html
<iframe id="Example2" title="Example2" width="400" height="300" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" src="http://25.30.9.23/testh265/3333_265.mp4">
</iframe>
```

