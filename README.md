# 调研市场游览器对MSE兼容情况
## 结论:
| Codec\Bowser       | Chrome | Chromium | Safari(intel) | Safari(M1) | Edge | Edge(old) | Firefox | IE |
| ------------------ | ------ | -------- | ------------- | ---------- | ---- | --------- | ------- | -- |
| FMP4/H.265/hev1    | No     | No       | Yes           | Yes        | No   | Yes       | No      | No |
| FMP4/H.265/hvc1    | No     | No       | Yes           | No         | No   | Yes       | No      | No |
| MP4/H.264/avc1     | Yes    | Yes      | Yes           | Yes        | Yes  | Yes       | No      | Yes |
| Webm/VP8           | Yes    | Yes      | No            | No         | Yes  | Yes       | Yes     | No |
| Webm/VP9           | Yes    | Yes      | No            | No         | Yes  | Yes       | No      | No |

