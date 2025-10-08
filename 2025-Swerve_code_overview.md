# FRC_Start

## 2025-Swerve: Competed in Shanghai Regional

### 1. clone source code
```
git clone --recurse-submodule https://github.com/ZodiacEFZ/2025-Swerve.git
```
---

### 2. �ؼ��ļ������ã������嵥��

Main.java��������ڣ����� RobotBase.startRobot(Robot::new) ���������ˡ�
Robot.java���̳� TimedRobot��ʵ�ֻ������������ڻص���init/periodic for disabled/auto/teleop/test��������ȫ�ֵ��ȡ�LED��������߲�ģʽ�л���Ϊ��
RobotContainer.java��������������������������ϵͳ��swerve��arm��intake��climber��limelight �ȣ���PathPlanner����ťӳ�䡢Ĭ������Զ�ѡ������Auto Chooser����
Constants.java����������ǰ������һ�� arm �켣ռλ����
�������� libzodiac �£��������⡢Ӳ������swerve ʵ�֡�PathPlanner ���ɡ�LED �����������ȡ�

---

### 3. �����������������У��������̣��𲽣�

1. JVM ������ִ�� Main.main������ RobotBase.startRobot(Robot::new)���� WPILib ��ܴ��� Robot ʵ������������ѭ����
2. Robot ���캯����
    ���� RobotContainer��������ϵͳ�������ťӳ���������ʼ������
    ��ʼ����ʱ����LED ��������LEDController.initInstance ��ע�� LED ���䣩��
3. Driver Station �����¼� driverStationConnected()��
    ���ز����������ļ���SuperMarioFlag-3.chrp����
    �������̴�����ʾ�����ӡ�
4. ������������ѭ�� robotPeriodic()��ÿ 20ms����
    ���� CommandScheduler.getInstance().run() ���� ����������ȡ���ť��ѯ����ϵͳ periodic()��
    ÿ 5 �봥��һ�� System.gc()���������գ���
    �� RobotContainer ���͵� SmartDashboard��
5. Disabled ģʽ��
    disabledInit()������ bot.brake()����ϵͳֹͣ/�ƶ��������� disabledTimer��
    disabledPeriodic()��1 ������ bot.shutdown()�������� LED ��ʾ������ɫ�����������������Ч����
6. Autonomous ģʽ��
    autonomousInit()��ֹͣ���֣��ƶ������ˣ���ȡ RobotContainer.getAutonomousCommand()������ PathPlanner �� chooser�������ǿ�����ȸ����
7. Teleop ģʽ��
    teleopInit()��ȡ���Զ�������������У���ֹͣ���֣��л����ֶ�����ģʽ��
8. Test ģʽ��
    testInit()��ֹͣ���֣���ȡ�������������е����CommandScheduler.cancelAll()����

---

### 4. �� RobotContainer �з�������Ҫ��ʼ����Ҫ�㣩

1. swerve ����������
    ��Ŀʹ�� TalonFXSwerve�����ѡ CTRESwerve�������ø���ģ��� CAN id����������PID���Ƕ�/���������ݱȡ��ְ뾶����ʼλ�˵ȡ�
    ���� PathPlanner ʵ��������������Լ����
2. Ĭ�����
    ���������� Translation2dSupplier����ҡ�ˣ��� Rotation2dSupplier����ҡ�ˣ��������������������ٶȿ�����ֱ�ӽǶȿ��ƣ��������������ṩ��Ĭ��������������Ϊ��ǰĬ�����
    ��������Climber����Ĭ��ʹ�� operator ��ҡ�� Y ���ƣ�getClimberOperationCommand����
3. ��ť-����ӳ�䣺
    ʹ�� CommandXboxController��driver��operator����ͨ�� onTrue/onFalse/onChange ������ӳ�䵽�ֱ۶�����ArmStage2 ��һϵ�����������/����Intake�����л� field-centric / direct-angle / slow-mode / zero heading��climber �л��ȡ�
4. �Ӿ��봫������
    ��ʼ�� Limelight��������Ч ID��Ĭ�� pipeline��������� swerve ������
5. ������
    ���������� SendableChooser������ PathPlanner���� SmartDashboard �Թ�����ѡ���Զ�����
    ��������ͷ�ɼ���CameraServer.startAutomaticCapture()����
    ���������ϵ����� TalonFX ������ϴ��� MusicPlayer�����õ������������TalonFX ���֣���

---

### 5. ��Ҫ��ϵͳ��ְ�𣨼��˵����

1. SwerveDrivetrain��TalonFXSwerve / CTRESwerve����
    ���� swerve ģ����ƣ��ṩ field-centric��direct-angle��slow-mode���㶨λ��·�����٣�PathPlanner���ȹ��ܡ�
2. Arm / ArmStage2��
    �����е�۶�λ��ץȡ/���¶��������Ԥ��λ�ã�L1/L2/L3/L4 �ȣ�����ת��̬���
3. Intake��
    ���ƽ���/����ֹͣ���
4. Climber��
    �����߼����ֶ����ƽӿڡ�
5. Limelight��
    �Ӿ�Ŀ����֧�֣����������Զ���������ϡ�
6. TalonFXMotor.MusicPlayer��
    ���õ����������Ԥ�������ļ���chrp����
7. LEDController��
    ���ƻ��� LED ������ʾ������ɫ��Ч����

---

---

### 6. ���������Ĭ��������Ϊ��Ҫ�㣩
1. CommandScheduler.run() �� robotPeriodic() �б����ã�����
    ��ѯ��ť�¼���onTrue/onFalse/onChange����
    ���Ⱥ�ִ�����������ϵͳ�� periodic()��
2. Ĭ�����
    ���û����������ռ����ϵͳ��������ά��Ĭ�ϼ�ʻ�����ȡ�ֱ����룩��
    Climber Ҳ��Ĭ�ϡ���������������Ӧҡ�����롣
3. �Զ��������� PathPlanner �� chooser��ͨ�� getAutonomousCommand() ���ز��� autonomousInit() �е��ȡ�

---

---

### 7. ��������·��ʾ�������ϵ絽 teleop��
1. �ϵ� -> Main.main() -> ���� Robot ʵ����
2. Robot() ���죺���� RobotContainer����ʼ��Ӳ��������Ĭ�������ť�󶨡�PathPlanner������ͷ������ϵͳ�ȡ�
3. Driver Station ���� -> ����������ʾ���ֲ��𶯿�������
4. ������ʼ��Autonomous����
    autonomousInit() ����ѡ�е��Զ����ִ�У�ͨ���� PathPlanner ·�����������
5. �Զ������򱻴�� -> ���� Teleop��
    teleopInit() ȡ���Զ����Driver/Operator ����ͨ������/ҡ�˿��� swerve��arm��intake��climber��
6. Disabled ģʽ�»� brake/shutdown ��ͨ�� LED ��ʾ������ɫ����

---

---

### 7. ��������·��ʾ�������ϵ絽 teleop��

1. ���ܵ�ע��� / �߽����
2. �Զ�����ȡ����teleopInit() ��ȡ���Զ������ϣ���Զ�����ֱ������ȷ�ж����޸Ĵ˴���
3. System.gc()������ÿ 5 �����һ�� GC������Ӱ��ʵʱ���ܣ�ͨ������Ӱ��С����ע����ʵʱ�����²��Ƽ�Ƶ��ǿ�� GC����
4. ������/������У׼��TalonFXSwerve ģ��Ա�������λ/���������� PID ������ǿ������ʵ���ϵ��Σ�ע����Ҳ�ᵽ TODO����
5. disabledPeriodic() �� 1s ����� shutdown()������ڽ����ڼ���Ҫ�ڸ���ʱ�䰲ȫ�ϵ���£�Ҫע�����ʱ��
6. PathPlanner ��ʼλ�������������г�ʼ pose ���գ��� PathPlanner ������ʱ���ã���ȷ��ѡ���Զ�·���ṩ��ȷ��ʼλ�ˡ�
7. ���豸�� CAN ID / �˿�������ʵӲ��ƥ�䣨�����ж��Ӳ�� ID ����ʽд������

---

---

### С�ᣨ�����뽨�飩

1. ����һ�����͵� Command-based���� swerve ������ PathPlanner Ϊ���ĵ� FRC ��Ŀ��
2. Main �� Robot ������������ �� RobotContainer ������ϵͳ��������������󶨡�
3. ���Ĺ��ܣ����� swerve ���ƣ�field-centric/direct-angle����PathPlanner �Զ�·����Arm/Intake/Climber ������ơ�Limelight ֧�֡�LED �����ַ�����Camera capture��
���飨С�Ľ�����
1. ��ʵ���϶� swerve ��ģ�� PID ������������ϵͳ��У׼���ѳ������е� TunerConstants��Constants��
2. �����Ƴ�������ǿ�� GC�����ڷ�ʵʱ�߳��ϴ����Ա���������ڶ�����
3. �ڹؼ���ϵͳ������ swerve�����Ӹ���ϸ�� telemetry�������������룩�����ڵ��Ա���ʱ���ϡ�
 
---