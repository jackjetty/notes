* 是Spring Boot提供的状态机的现成实现。
* 理论（有点像工作流）
    * 需要定义一些状态的枚举，以及一些引起状态变化的事件的枚举。
    * 每个状态可以对应的创建一个继承自org.springframework.statemachine.action.Action的类，用来在重写的execute方法中做动作并且通过sendEvent触发状态改变到下一状态。
    * 那么在切换到下一状态时，就会自动触发相应的Action
* 参考
    * [通过spring statemmachine 自定义构建属于自己的状态机（两种方式）](https://blog.csdn.net/m0_37138008/article/details/77141630)
    * [使用Spring StateMachine框架实现状态机](https://blog.csdn.net/m0_37138008/article/details/74858122)
* 使用
    * 定义状态、事件的枚举

```
public enum SimulatorStates
{
    IDLE,
    ASSET_CREATION,
    ONBOARDING,
    ASSET_CONFIGURATION,
    TEMPLATE_ASSIGNMENT,
    STOP,
    ERROR
}

public enum SimulatorStateMachineEvent
{
    IDLE,
    CREATE_ASSET,
    ONBOARD,
    CONFIGURE_ASSET,
    ASSIGN_TEMPLATE,
    STOP,
    RE_IDLE,
    ERROR
}

```

    * 配置/构建状态机
        * 方式一
            * 使用StateMachineFactory
```
ObjectStateMachineFactory<String, String> machineFactory = new ObjectStateMachineFactory<String, String>(machineModel);
StateMachine<String, String> stateMachine  = machineFactory.getStateMachine();
```
        * 方式二
            * 使用StateMachineBuilder
        * 方式三
            * **使用配置类配置状态机，如每个状态State对应的处理逻辑Action，什么事件Event会导致哪些状态State间切换，）**

```
@Configuration
@EnableStateMachineFactory
public class StateMachineConfiguration extends EnumStateMachineConfigurerAdapter<SimulatorStates, SimulatorStateMachineEvent>
{
    @Override
    public void configure(StateMachineStateConfigurer<SimulatorStates, SimulatorStateMachineEvent> states) throws Exception {
        states.withStates().initial(SimulatorStates.IDLE)
				.state(SimulatorStates.IDLE, getIdleAction())
				.state(SimulatorStates.ASSET_CREATION, getAssetCreationAction())
				.state(SimulatorStates.ONBOARDING, getOnboardingAction())
				.state(SimulatorStates.ASSET_CONFIGURATION, getAssetConfigurationAction())
				.state(SimulatorStates.TEMPLATE_ASSIGNMENT, getTemplateAssignmentAction())
				.state(SimulatorStates.STOP, getStopAction())
				.state(SimulatorStates.ERROR, getErrorAction());
    }

    @Override
    public void configure(StateMachineTransitionConfigurer<SimulatorStates, SimulatorStateMachineEvent> transitions) throws Exception {

        transitions.
				withExternal().source(SimulatorStates.IDLE).target(SimulatorStates.ASSET_CREATION).event(SimulatorStateMachineEvent.CREATE_ASSET).and().
				withExternal().source(SimulatorStates.ASSET_CREATION).target(SimulatorStates.ONBOARDING).event(SimulatorStateMachineEvent.ONBOARD).and().
				withExternal().source(SimulatorStates.ONBOARDING).target(SimulatorStates.ASSET_CONFIGURATION).event(SimulatorStateMachineEvent.CONFIGURE_ASSET).and().
				withExternal().source(SimulatorStates.ASSET_CONFIGURATION).target(SimulatorStates.TEMPLATE_ASSIGNMENT).event(SimulatorStateMachineEvent.ASSIGN_TEMPLATE).and().
				withExternal().source(SimulatorStates.TEMPLATE_ASSIGNMENT).target(SimulatorStates.STOP).event(SimulatorStateMachineEvent.STOP).and().
				withExternal().source(SimulatorStates.STOP).target(SimulatorStates.IDLE).event(SimulatorStateMachineEvent.RE_IDLE).and().

				withExternal().source(SimulatorStates.ASSET_CREATION).target(SimulatorStates.ERROR).event(SimulatorStateMachineEvent.ERROR).and().
				withExternal().source(SimulatorStates.ONBOARDING).target(SimulatorStates.ERROR).event(SimulatorStateMachineEvent.ERROR).and().
				withExternal().source(SimulatorStates.ASSET_CONFIGURATION).target(SimulatorStates.ERROR).event(SimulatorStateMachineEvent.ERROR).and().
				withExternal().source(SimulatorStates.TEMPLATE_ASSIGNMENT).target(SimulatorStates.ERROR).event(SimulatorStateMachineEvent.ERROR).and().
				withExternal().source(SimulatorStates.STOP).target(SimulatorStates.ERROR).event(SimulatorStateMachineEvent.ERROR).and().
				withExternal().source(SimulatorStates.ERROR).target(SimulatorStates.IDLE).event(SimulatorStateMachineEvent.RE_IDLE);
    }

	@Bean(name = "stateMachineTaskScheduler")
	public ConcurrentTaskScheduler myStateMachineTaskScheduler() {
		ScheduledThreadPoolExecutor threadPool = new ScheduledThreadPoolExecutor(4);
		return new ConcurrentTaskScheduler(threadPool);
	}

    @Override
    public void configure(StateMachineConfigurationConfigurer<SimulatorStates, SimulatorStateMachineEvent> config) throws Exception {
        config.withConfiguration().taskScheduler(myStateMachineTaskScheduler());
    }

    @Bean
    Action<SimulatorStates, SimulatorStateMachineEvent> getIdleAction() {
        return new Idle();
    }
    
    @Bean
    Action<SimulatorStates, SimulatorStateMachineEvent> getAssetCreationAction() {
        return new AssetCreation();
    }

    @Bean
    Action<SimulatorStates, SimulatorStateMachineEvent> getOnboardingAction() {
        return new Onboarding();
    }
    
    @Bean
    Action<SimulatorStates, SimulatorStateMachineEvent> getAssetConfigurationAction() {
        return new AssetConfiguration();
    }

	@Bean
	Action<SimulatorStates, SimulatorStateMachineEvent> getTemplateAssignmentAction() {
		return new TemplateAssignment();
	}

    @Bean
    Action<SimulatorStates, SimulatorStateMachineEvent> getStopAction() {
        return new StopAction();
    }

    @Bean
    Action<SimulatorStates, SimulatorStateMachineEvent> getErrorAction() {
        return new ErrorAction();
    }
}
```

    * 启动状态机
        * stateMachine.start();

```
@Component
public class StateExecutor
{
    @Autowired
    private StateMachineFactory<SimulatorStates, SimulatorStateMachineEvent> stateMachineFactory;

    public void runStates(AssetTestInfo assetTestInfo) {
        StateMachine<SimulatorStates,SimulatorStateMachineEvent> stateMachine = stateMachineFactory.getStateMachine();
        stateMachine.getExtendedState().getVariables().put(SimulatorConstants.ASSET_TEST_INFO, assetTestInfo);
        stateMachine.start();
    }
}
```
    * 定义Action处理逻辑
        * 设置变量？
            * stateMachine.getExtendedState().getVariables().put(SimulatorConstants.ASSET_TEST_INFO, assetTestInfo);
        * 发送/触发事件
            * stateMachine.sendEvent(Events.PAY);
            * context.getStateMachine().sendEvent(SimulatorStateMachineEvent.CONFIGURE_ASSET);

```
public class AssetConfiguration implements Action<SimulatorStates, SimulatorStateMachineEvent>
{
    private static final Logger logger = LoggerFactory.getLogger(AssetConfiguration.class);

    @Autowired
    private AssetConfigurationService assetConfigurator;

    @Override
    public void execute(StateContext<SimulatorStates, SimulatorStateMachineEvent> context)
    {
        AssetTestInfo assetTestInfo = AssetTestInfo.class.cast(context.getExtendedState().getVariables().get(SimulatorConstants.ASSET_TEST_INFO));

        logger.debug("Asset Configuration State Start for asset: {}", assetTestInfo.getAssetId());
        try {
            assetConfigurator.execute(assetTestInfo);
            context.getStateMachine().sendEvent(SimulatorStateMachineEvent.ASSIGN_TEMPLATE);
        } catch (Exception e) {
            logger.error("Cannot complete asset configuration", e);
            context.getStateMachine().sendEvent(SimulatorStateMachineEvent.ERROR);
        }

		logger.debug("Asset Configuration State End for asset: {}", assetTestInfo.getAssetId());
    }
}
```