# 调研市场游览器对MSE兼容情况
## 结论:
| Codec\Browser      | Chrome | Chromium | Safari(Intel) | Safari(M1) | Edge | Edge(Old) | Firefox | IE |
| ------------------ | ------ | -------- | ------------- | ---------- | ---- | --------- | ------- | -- |
| FMP4/H.265/hev1    | No     | No       | Yes           | Yes        | No   | Yes       | No      | No |
| FMP4/H.265/hvc1    | No     | No       | No            | No         | No   | Yes       | No      | No |
| MP4/H.264/avc1     | Yes    | Yes      | Yes           | Yes        | Yes  | Yes       | No      | Yes |
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
- 系统 Windows 10 1803 / MacOS Catalina / MacOS Big Sur
- 游览器版本 Chrome92+ / Chromium92+(all-codec+) / Safari(Intel) / Safari(M1) / Edge92+ / Edge(Old)42(EdgeHTML 17.17134) / Firefoxf91 / IE11
- 编码环境 Windows10 均未安装HEVC 所以暂时无法验证CPU硬解 原因: 收费
- Windows 采用显卡硬解 MacOS 采用CPU硬解

## 测试用例:
1. 监控摄像头拍摄 MP4/H.265/hvc1 的视频 5分钟
2. 标准 MP4/H.264/avc1 视频 24 分钟

## 测试方法:
1. 将视频1分别转码为FMP4(H.265/hev1 H.264/avc1) Webm(VP8 VP9) 使用MSE播放指定编解码播放
2. 将视频2分别转码为FMP4(H.265/hev1 H.264/avc1) Webm(VP8 VP9) 使用MSE播放指定编解码播放
3. 设置的编解码有 
  - video/mp4; codecs="hvc1" 
  - video/mp4; codecs="hev1"
  - video/mp4; codecs="avc1.42E01E, mp4a.40.2"
  - video/webm; codecs="vp8"
  - video/webm; codecs="vp9"
 
 




