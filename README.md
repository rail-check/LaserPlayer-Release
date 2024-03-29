# LaserPlayer
v1.8.2


## 命令行格式

### 参数说明

程序名称 `laser_player.exe`，命令行参数如下：

| 参数                    | 含义                                                 |
| ----------------------- | :--------------------------------------------------- |
| --help                  | 显示帮助信息                                         |
| --version               | 显示程序版本。                                       |
| --in arg                | 输入数据文件目录。                                   |
| --calib arg             | 输入标定参数文件名称。                               |
| --rail-std arg (=60)    | 轨道标准，50 或 60。                                 |
| --kod arg (=1)          | 里程计刻度系数，单位：m/tick。                       |
| --trj-step arg (=0.625) | 输出轨迹步长，单位：m。                              |
| --no-gnss               | 没有GNSS，只使用里程计。                             |
| --manpos arg            | 初始位置，输入顺序为：纬度（°）经度（°） 高度（m）。 |
| --no-contour-extraction | 不做廓形提取。                                       |
| --untidy                | 不清理过程文件。                                     |

### 示例

```bash
laser_player.exe --help
```

### LOG 文件

在程序所在目录下的 `log` 文件夹包含历次处理的记录文件，会占用空间，可以清理。

## GUI操作说明

程序名称 `laser_player_gui.exe`。用来检查激光和特征提取情况。

<img src="./README.assets/image-20230217181923788.png" alt="image-20230217181923788" style="zoom:50%;" />

- `左右键` —— 以单帧步进；

- `shift+左右键` —— 以10帧步进；

- `ctrl+左右键` —— 以100帧步进；

- `ctrl+shift+左右键` —— 以1000帧步进；

- `r` —— 恢复视角；

- `o` —— 切换透视/正视（在正视图下，恢复视角有 bug，先切换到透视图，恢复视角后，再切换回正视图）；

- `激光器编号` —— `0`——左前，`2`——右前，`3`——左后，`1`——右后，`Calibrated`  —— 校正后同时显示4个廓形；

- `钢轨标准` —— 可以选择 50kg 或 60kg，默认 60kg。

- `打开文件夹` —— 打开包含激光器数据的文件夹下所有的文件，激光器编号自动从文件名中提取，相同编号的激光器数据会拼接到一起；

- `校正` —— 读取标定参数文件（`.yaml`），对点云位姿进行校正。校正后会在下来菜单中增加 `Calibrated` 选项，选中后会同时显示所有点云的相对位置，坐标原点代表惯导系统测量中心。

  `.yaml` 文件格式如下，可以由 `beam_calibr` 生成，下载地址 [rail-check/BeamCalib-Release: 轨检梁标定程序 (github.com)](https://github.com/rail-check/BeamCalib-Release)。

  ```yaml
  %YAML:2.0
  ---
  # 安装误差
  Laser0:
     Transform: !!opencv-matrix
        rows: 4
        cols: 4
        dt: f
        data: [ 8.08076918e-01, 5.89076757e-01, 0., -7.35984436e+02,
            -5.89076757e-01, 8.08076918e-01, 0., -3.01532349e+02, 0., 0.,
            1., -2.68500000e+02, 0., 0., 0., 1. ]
     Inverse: 1
  Laser1:
     Transform: !!opencv-matrix
        rows: 4
        cols: 4
        dt: f
        data: [ 8.35906982e-01, -5.48871040e-01, 0., 7.25698608e+02,
            5.48871040e-01, 8.35906982e-01, 0., -3.24404480e+02, 0., 0.,
            1., 2.68500000e+02, 0., 0., 0., 1. ]
     Inverse: 0
  Laser2:
     Transform: !!opencv-matrix
        rows: 4
        cols: 4
        dt: f
        data: [ 8.33538294e-01, -5.52462459e-01, 0., 7.23198914e+02,
            5.52462459e-01, 8.33538294e-01, 0., -3.24730316e+02, 0., 0.,
            1., -2.68500000e+02, 0., 0., 0., 1. ]
     Inverse: 1
  Laser3:
     Transform: !!opencv-matrix
        rows: 4
        cols: 4
        dt: f
        data: [ 8.13677609e-01, 5.81317306e-01, 0., -7.36984558e+02,
            -5.81317306e-01, 8.13677609e-01, 0., -3.04212616e+02, 0., 0.,
            1., 2.68500000e+02, 0., 0., 0., 1. ]
     Inverse: 0
  ```

- `提取` —— 对轨道廓形和特征进行提取。

- 点云图像注释

  <img src="./README.assets/image-20230216221543510.png" alt="image-20230216221543510" style="zoom: 50%;" />

## 关于程序中提示信息的说明

### 左（/右）前（/后）方提取错误，使用上一次遮罩点

未能从对应位置激光器扫描数据中提取出轨道廓形，具体会给出以下三种提示：

- 匹配点数过少
- 旋转角度过大
- 匹配翻转

可能的原因是激光廓形不完整、异物或有害空间等，将尝试使用上一次的匹配参数重新匹配。

### 左（/右）前（/后）方提取错误，使用初始遮罩点

尝试使用上一次的匹配参数重新匹配失败，使用初始参数重新匹配。

### 左（/右）前（/后）方提取失败

使用初始匹配参数匹配再次失败，将使用上一次匹配成功的结果作为本次结果。

### 前（/后）激光器组特征提取错误

对提取后的轨道廓形进行轨道特征提取，对应位置的激光器未能提取成功。

可能的原因是特征点附近激光点数过少，特征坐标计算异常。将使用上一次的结果作为本次结果。
