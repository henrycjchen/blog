**物理像素**：设备屏幕实际拥有的像素点。比如 iPhone 6 的屏幕在宽度方向有 750 个像素点，高度方向有 1334 个像素点，所以 iPhone 6 总共有 750*1334 个物理像素。

**逻辑像素`px`**：也叫“设备独立像素”（Device Independent Pixel, DIP），可以理解为反映在 CSS/JS 代码里的像素点数。

**设备像素比**（Device Pixel Ratio, **DPR**）：一个设备的物理像素与逻辑像素之比。可由 `window.devicePixelRatio` 获取到



**小程序 `rpx`**：这是一个比例单位，表示`屏幕宽度px/750`。所以在 iphone6 下，1rpx = 375px/750 = 0.5px;