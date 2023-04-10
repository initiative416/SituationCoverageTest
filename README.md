## 基于交叉口情境覆盖的场景测试代码环境搭建说明

## 1 环境安装

### 1.1 版本

- python>=3.7

- [carla==0.9.10](https://mirrors.sustech.edu.cn/carla/ )
- [Scenario Runner==0.9.10（和carla版本保持一致）](https://link.zhihu.com/?target=https%3A//github.com/carla-simulator/scenario_runner/releases)

### 1.2 Carla安装

[Ubuntu 20.04安装Carla 0.9.10](https://blog.csdn.net/weixin_43149506/article/details/126337966)

1. 需要pip3的版本大于20.3，如果需要更新

   ```shell
   #for python 3
   pip3 -V
   #for python 2
   pip -V
   
   #for python 3
   pip3 install --upgrade pip
   #for python 2
   pip install --upgrade pip
   ```

2. 安装pygame numpy

   ```shell
   #for python 2
   pip install --user pygame numpy
   #for python 3
   pip3 install --user pygame numpy
   ```

3. 下载并安装Carla

   链接：https://mirrors.sustech.edu.cn/carla/

   ![image-20230410143344448](http://github.com/initiative416/SituationCoverageTest/raw/main/image-20230410143344448.png)

   下载完毕后将两个压缩包解压后，放在新创建的CARLA_0.9.10.1文件夹中

4. 将AddiionalMaps_0.9.10.1.tar.gz包放入Import中

   ```shell
   cd path/to/carla/root
   ./ImportAssets.sh
   ```

5. 运行Carla

   ```shell
   cd path/to/carla/root
   ./CarlaUE4.sh
   ```

### 1.3 Scenario Runner安装

1. 需要下载和carla版本一致的[Scenario Runner(SR)](https://link.zhihu.com/?target=https%3A//github.com/carla-simulator/scenario_runner/releases)

2. 安装依赖：建议使用conda环境

   ```shell
   cd xxx/scenario_runner-0.9.10
   
   #Python 3.x
   sudo apt remove python3-networkx #if installed, remove old version of networkx
   pip3 install --user -r requirements.txt
   ```

3. 添加环境变量

   ```shell
   #carla 0.9.10
   export CARLA_ROOT=/path/to/your/carla/installation
   export PYTHONPATH=$PYTHONPATH:${CARLA_ROOT}/PythonAPI/carla/dist/carla-0.9.10-py3.7-linux-x86_64.egg:${CARLA_ROOT}/PythonAPI/carla/agents:${CARLA_ROOT}/PythonAPI/carla
   export SCENARIO_RUNNER_PATH=/path/to/your/scenario_runner/installation
   ```

4. 测试安装是否成功

   ```shell
   ./CarlaUE4.sh
   
   python scenario_runner.py --scenario FollowLeadingVehicle_1 --reloadWorld
   python manual_control.py
   ```

### 1.4 搭建Situation Coverage-based AV-Testing框架

1. [下载github上对应项目文件](https://github.com/zaidtahirbutt/Situation-Coverage-based-AV-Testing-Framework-in-CARLA)

   ![image-20230410144902547](/home/lulu/.config/Typora/typora-user-images/image-20230410144902547.png)

2. 到Carla安装目录下，将项目中clone的PythonAPI文件替换原Carla中的PythonAPI文件

3. 将项目中clone的scenario_runner-0.9.10文件替换原scenario_runner-0.9.10文件

4. 安装packages

   ```shell
   #method 1
   pip install -r requirements.txt
   
   #method 2
   #安装以下package
   pip install xmlschema
   pip install six
   pip install py-trees
   pip install ephem
   pip install networkx
   conda install -c conda-forge shapely
   pip install tabulate
   pip install XlsxWriter
   #该代码使用Google Object Detection API，该API使用Tensorflow模型进行对象检测，需要安装以下包
   pip install scipy
   pip install tensorflow
   pip install matplotlib
   pip install opencv-python
   pip install openpyxl
   pip install cython
   pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
   pip install tf_slim
   ```

5. 修改~/.bashrc文件

   ```shell
   #carla 0.9.10.1-sit_carla
   export CARLA_ROOT=/path/to/CARLA_0.9.10.1
   export PYTHONPATH=$PYTHONPATH:${CARLA_ROOT}/PythonAPI/carla/dist/carla-0.9.10-py3.7-linux-x86_64.egg:${CARLA_ROOT}/PythonAPI/carla/agents:${CARLA_ROOT}/PythonAPI/carla:/path/to/scenario_runner/models:/path/to/scenario_runner/models/research:/path/to/scenario_runner/models/research/slim:${CARLA_ROOT}/PythonAPI
   export SCENARIO_RUNNER_PATH=/path/to/scenario_runner/
   ```

### 1.5 运行Situation Coverage-based AV-Testing框架

1. 单次运行，在scenario_runner-0.9.10文件夹下运行

   ```shell
   python situationcoverage_AV_VV_Framework.py --scenario IntersectionScenarioZ_11  --not_visualize --Activate_IntersectionScenario_Seed --IntersectionScenario_Seed 13212 --use_sit_cov --reloadWorld --output
   ```

   - 可更改参数项--IntersectionScenario_Seed后的随机数（例子为13212），以获得不同的起始随机种子，以便获得不同的结果集
   - 如果想随机生成情境而不是基于情境覆盖的生成，可以通过删除参数项--use_sit_cov实现

2. 多次运行，例子中为运行三次

   ```shell
   python situationcoverage_AV_VV_Framework.py --scenario IntersectionScenarioZ_11  --not_visualize --Activate_IntersectionScenario_Seed --IntersectionScenario_Seed 13212 --use_sit_cov --reloadWorld --output --repetitions 3
   ```

## 2 可能遇到的问题

<img src="https://user-images.githubusercontent.com/25262332/147320344-826c573b-4016-4d0a-b0fa-434f76c82040.png">

1. [解决：Module Not Found Error: No module named 'object_detection'](https://blog.csdn.net/liubing8609/article/details/115264592)

   ```shell
   #需要将scenario_runner-0.9.10/models/research/object_detection/packages/tf2文件下的setup.py复制到scenario_runner-0.9.10/models/research目录下，然后执行以下命令
   python setup.py build
   python setup.py install
   ```

2. [解决：缺少动态链接库.so(cannot open shared object file: No siuch file or directory)解决办法](https://blog.csdn.net/Kena_M/article/details/108349698)

   ```shell
   #相关依赖库已经安装或编译了，但是大部分默认存放在/usr/local/lib中，而linux系统通常只会去/usr/lib中寻找库文件，这就导致无法加载库文件导致报错
   #method 1：使用ln命令对*.so文件创建链接，放到/usr/lib中
   ln -s /your install path/xxx.so /usr/lib
   sudo ldconfig
   
   #method 2：直接将*.so文件复制到/usr/lib中
   ```

