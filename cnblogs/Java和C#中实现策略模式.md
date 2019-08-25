* C#中也是类似做法，都是**通过反射来建立一个Map或者Dictionary**，存储的是type字符串和该type的一个对象的key-value值，用于在使用者/Context类根据type字符串来获取相应的service处理类并使用其处理函数，这些service处理类都实现了同样的处理接口，只是该处理函数的具体实现逻辑不同
* Java的实现
    * 通常的用途是根据type字符串去获取service类，再进行调用处理
    * 这里的做法有点不一样，主要是因为目的不同（根据传入的参数对象中是一些boolean类型的开关字段，需要根据这些开关字段的状态来确定一些逻辑是否需要运行），因此是循环所有的service类，如果当前service类对应的开关是开着的，那么就调用处理
    * 其实感觉也可以像传统的做法那样，先循环所有的开关，如果打开着，就去map中找到对应的service类并调用处理函数

```
// auto wired all service objects
@Autowired
private List<AssetConfigTypeService> configList;


// build map
Map<String, Object> dtoJson = convertToMapUsingJsonPropertyNamesAsKeys(mindConnectConfigurationDto);

    private Map<String, Object> convertToMapUsingJsonPropertyNamesAsKeys(Object anObj)
    {
        ObjectMapper mapper = new ObjectMapper();
        return mapper.convertValue(anObj, new TypeReference<Map<String, Object>>()
        {
        });
    }


// loop all service object list and run some of them who's toggle is on
        Map<String, Object> dtoJson = convertToMapUsingJsonPropertyNamesAsKeys(mindConnectConfigurationDto);
        for (AssetConfigTypeService assetConfigType : configList)
        {
            Object boolObj = dtoJson.get(assetConfigType.getConfigTypeJsonCorrelation().getJsonPropertyName());
            if (boolObj instanceof Boolean)
            {
                boolean isConfigActive = ((Boolean) boolObj).booleanValue();
                if (isConfigActive)
                {
                    aspectTypeList.addAll(assetConfigType.getAspectTypeListFromConfig(connectivityDto));
                }
            }
            else
            {
                LOG.error("Boolean type is expected!");
                throw new IllegalArgumentException("Boolean type is expected!");
            }
        }
```