# FRC_Start

## 2025-Swerve: Competed in Shanghai Regional

### 1. clone source code
```
git clone --recurse-submodule https://github.com/ZodiacEFZ/2025-Swerve.git
```
---

### 2. 关键文件与作用（快速清单）

Main.java：程序入口，调用 RobotBase.startRobot(Robot::new) 启动机器人。
Robot.java：继承 TimedRobot，实现机器人生命周期回调（init/periodic for disabled/auto/teleop/test），负责全局调度、LED、音乐与高层模式切换行为。
RobotContainer.java：机器人容器，创建并配置子系统（swerve、arm、intake、climber、limelight 等）、PathPlanner、按钮映射、默认命令、自动选择器（Auto Chooser）。
Constants.java：常量（当前仅包含一项 arm 轨迹占位）。
其它（在 libzodiac 下）：驱动库、硬件抽象、swerve 实现、PathPlanner 集成、LED 与音乐驱动等。

---

### 3. 主函数（启动到运行）总体流程（逐步）

1. JVM 启动后执行 Main.main：调用 RobotBase.startRobot(Robot::new)，由 WPILib 框架创建 Robot 实例并进入运行循环。
2. Robot 构造函数：
    创建 RobotContainer（所有子系统、命令、按钮映射在这里初始化）。
    初始化定时器、LED 控制器（LEDController.initInstance 并注册 LED 区间）。
3. Driver Station 连接事件 driverStationConnected()：
    加载并播放音乐文件（SuperMarioFlag-3.chrp）。
    控制器短促震动提示已连接。
4. 机器人周期性循环 robotPeriodic()（每 20ms）：
    调用 CommandScheduler.getInstance().run() —— 负责命令调度、按钮轮询与子系统 periodic()。
    每 5 秒触发一次 System.gc()（垃圾回收）。
    将 RobotContainer 发送到 SmartDashboard。
5. Disabled 模式：
    disabledInit()：调用 bot.brake()（子系统停止/制动）、启动 disabledTimer。
    disabledPeriodic()：1 秒后调用 bot.shutdown()；持续用 LED 显示联盟颜色（红或蓝）并做呼吸效果。
6. Autonomous 模式：
    autonomousInit()：停止音乐，制动机器人，获取 RobotContainer.getAutonomousCommand()（来自 PathPlanner 的 chooser），若非空则调度该命令。
7. Teleop 模式：
    teleopInit()：取消自动命令（若仍在运行），停止音乐，切换到手动控制模式。
8. Test 模式：
    testInit()：停止音乐，并取消所有正在运行的命令（CommandScheduler.cancelAll()）。

---

### 4. 在 RobotContainer 中发生的主要初始化（要点）

1. swerve 驱动创建：
    项目使用 TalonFXSwerve（或可选 CTRESwerve），配置各个模块的 CAN id、编码器、PID（角度/驱动）、齿比、轮半径、初始位姿等。
    创建 PathPlanner 实例并传入驱动与约束。
2. 默认命令：
    驱动：基于 Translation2dSupplier（左摇杆）与 Rotation2dSupplier（右摇杆）构造两类输入流（角速度控制与直接角度控制），并将由驱动提供的默认驱动命令设置为当前默认命令。
    爬升器（Climber）：默认使用 operator 左摇杆 Y 控制（getClimberOperationCommand）。
3. 按钮-命令映射：
    使用 CommandXboxController（driver、operator）并通过 onTrue/onFalse/onChange 将按键映射到手臂动作（ArmStage2 的一系列命令）、进给/出球（Intake）、切换 field-centric / direct-angle / slow-mode / zero heading、climber 切换等。
4. 视觉与传感器：
    初始化 Limelight，设置有效 ID、默认 pipeline，并将其和 swerve 关联。
5. 其他：
    创建并放入 SendableChooser（来自 PathPlanner）到 SmartDashboard 以供比赛选择自动程序。
    开启摄像头采集（CameraServer.startAutomaticCapture()）。
    将机器人上的所有 TalonFX 电机集合传给 MusicPlayer，可用电机播放音符（TalonFX 音乐）。

---

### 5. 主要子系统与职责（简短说明）

1. SwerveDrivetrain（TalonFXSwerve / CTRESwerve）：
    四驱 swerve 模块控制，提供 field-centric、direct-angle、slow-mode、零定位、路径跟踪（PathPlanner）等功能。
2. Arm / ArmStage2：
    负责机械臂定位、抓取/放下动作、多个预设位置（L1/L2/L3/L4 等）、旋转姿态命令。
3. Intake：
    控制进球/出球及停止命令。
4. Climber：
    爬升逻辑与手动控制接口。
5. Limelight：
    视觉目标检测支持，与驱动或自动化对齐配合。
6. TalonFXMotor.MusicPlayer：
    利用电机发声播放预置音乐文件（chrp）。
7. LEDController：
    控制机体 LED 区域，显示联盟颜色与效果。

---

---

### 6. 命令调度与默认命令行为（要点）
1. CommandScheduler.run() 在 robotPeriodic() 中被调用，负责：
    轮询按钮事件（onTrue/onFalse/onChange）。
    调度和执行命令，调用子系统的 periodic()。
2. 默认命令：
    如果没有其他命令占用子系统，驱动会维持默认驾驶命令（读取手柄输入）。
    Climber 也有默认“操作”命令以响应摇杆输入。
3. 自动命令来自 PathPlanner 的 chooser，通过 getAutonomousCommand() 返回并在 autonomousInit() 中调度。

---

---

### 7. 典型运行路径示例（从上电到 teleop）
1. 上电 -> Main.main() -> 启动 Robot 实例。
2. Robot() 构造：创建 RobotContainer、初始化硬件、设置默认命令、按钮绑定、PathPlanner、摄像头、音乐系统等。
3. Driver Station 连接 -> 播放连接提示音乐并震动控制器。
4. 比赛开始（Autonomous）：
    autonomousInit() 调用选中的自动命令并执行（通常是 PathPlanner 路径跟踪命令）。
5. 自动结束或被打断 -> 进入 Teleop：
    teleopInit() 取消自动命令，Driver/Operator 可以通过按键/摇杆控制 swerve、arm、intake、climber。
6. Disabled 模式下会 brake/shutdown 并通过 LED 显示联盟颜色。。

---

---

### 7. 典型运行路径示例（从上电到 teleop）

1. 可能的注意点 / 边界情况
2. 自动命令取消：teleopInit() 会取消自动命令，若希望自动持续直到被明确中断需修改此处。
3. System.gc()：代码每 5 秒调用一次 GC，可能影响实时性能（通常短期影响小，但注意在实时控制下不推荐频繁强制 GC）。
4. 传感器/编码器校准：TalonFXSwerve 模块对编码器相位/比例因子与 PID 依赖较强，需在实车上调参（注释中也提到 TODO）。
5. disabledPeriodic() 在 1s 后调用 shutdown()：如果在禁用期间需要在更短时间安全断电或保温，要注意该延时。
6. PathPlanner 初始位姿依赖：构造中初始 pose 留空（用 PathPlanner 在运行时设置），确保选的自动路径提供正确起始位姿。
7. 多设备的 CAN ID / 端口需与真实硬件匹配（代码中多个硬件 ID 已显式写出）。

---

---

### 小结（结论与建议）

1. 这是一个典型的 Command-based、以 swerve 驱动与 PathPlanner 为核心的 FRC 项目：
2. Main → Robot 管理生命周期 → RobotContainer 构建子系统、命令与控制器绑定。
3. 核心功能：四轮 swerve 控制（field-centric/direct-angle）、PathPlanner 自动路径、Arm/Intake/Climber 命令化控制、Limelight 支持、LED 与音乐反馈、Camera capture。
建议（小改进）：
1. 在实机上对 swerve 各模块 PID 与编码器标度做系统化校准并把常数集中到 TunerConstants／Constants。
2. 考虑移除或限制强制 GC（或在非实时线程上处理）以避免控制周期抖动。
3. 在关键子系统（例如 swerve）增加更详细的 telemetry（电流、错误码），便于调试比赛时故障。
 
---