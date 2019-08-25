* MindSphere账号管理-Tenant
    * 分为developer、operator、iot三种tenant账号类型
    * developer tenant是给开发测试用的，也用来推送APP到operator tenant，包含一套cloud foundry环境和一个账号（还可以有子账号，可以用于配置和访问，不需要provisioning就可以访问，只要当前登录用户分配了该APP的Role即可，如果代码scope有检查，则必须分配有这个scope权限的APP的Role）
    * operator tenant是给线上运行的，是服务端，APP都是从developer tenant推送过来的，包含一套cloud foundry环境和一个账号（还可以有子账号，可以用于配置和访问，不需要provisioning就可以访问，只要当前登录用户分配了该APP的Role即可，如果代码scope有检查，则必须分配有这个scope权限的APP的Role）
    * iot tenant是给用户使用的，用户有了这个账号，才可能购买和使用APP，购买了以后需要operator tenant provisioning给这个iot tenant，这个iot tenant才能使用
    * 每种tenant都可以自行配置子账号和权限
* MindSphere App和CloudFoundry Component的映射和配合使用
    * MindSphere App和CloudFoundry Component通过在developer cockpit上的scope设置及分配到哪个Role、Settings页面的User的Role分配，来控制权限分配。
    * 一个MindSphere App可以对应多个CloudFoundry Component，因此这多个CloudFoundry Component可以共用一组scope权限检查
    * 即使后台代码中没有在接口上检查scope权限，也需要访问者（developer tenant、operator tenant、iot tenant）有这个App的权限，否则无法访问该CloudFoundry Component注册在MindSphere上的url pattern。
        * iot tenant需要有user或者admin的Role？
* MindSphere APP类型
    * app
        * 默认的类型，暴露给用户的UI类型（显示在launchpad上）
    * api
        * 仅供前端、设备调用的（不示在launchpad上）
    * plugin
        * 作为MindSphere自带的默认APP的插件，不显示在launchpad上）
        * UI-PLUGIN-ASSETMANAGER
        * 还有用在fleet manager等上插件类型
* MindSphere APP在developer tenant每个版本会有一个图标，但在operator tenant上只有一个最新的
* operator tenant上每个用户只需要provisioning一次APP，APP升级不需要再做了
* 网络请求
    * 对外接口
        * CloudFoundry Component注册到MindSphere APP上之后，才能通过iottenantname-appname-operatorname.cn1.mindsphere-in.cn/xxx/xxx访问
            * 如果为了不让APP显示在launchpad上，修改appType为api了，那么请求地址会变为iottenantname-appname-operatorname.cn1.mindsphere-in.cn/api/targetappname-operatorname/xxx/xxx
            * 不管app类型的地址，还是api类型的接口地址，都是给前端来调用的，在通过平台的认证时会用到登录后才有的Cookie中的认证信息
            * 需要MindSphere平台的人帮忙手动调接口来修改appType
            * 会同时自动注册到MindSphere Gateway上，地址如gateway.cn1.mindsphere-in.cn/appname-operatorname/appversion/xxx/xxx
                * appverison在developer tenant上一直是v1，而在operator tenant上默认就是APP当前的version，如1.6.2，因此有时为了代码方便调用，需要平台的人帮忙改为固定值，如v3（有另一个问题，从operator tenant provisioning给iot tenant时必须接口和注册时的版本一致，因此每次有新用户都要平台的人帮忙先改成一致的1.6.2的版本号，provisioning过去，然后再改成v3...）
        * CloudFoundry Component也可以直接作为api注册到MindSphere Gateway上，就可以通过Gateway访问了：gateway.cn1.mindsphere-in.cn/api/appname-operatorname/apiversion/xxx/xxx
                * apiverison在developer tenant上一直是v1，而在operator tenant上默认就是APP当前的version，如1.6.2，因此有时为了代码方便调用，需要平台的人帮忙改为固定值，如v3（有另一个问题，从operator tenant provisioning给iot tenant时必须接口和注册时的版本一致，因此每次有新用户都要平台的人帮忙先改成一致的1.6.2的版本号，provisioning过去，然后再改成v3...）
            * 需要MindSphere平台的人帮忙手动调接口来注册接口
            * 还可以同时将接口设置为unsecured的，那么在访问时就不需要登录了
        * 不通过以上方式的话，CloudFoundry Component对外就是不可访问的，即没有通过各种方式注册在MindSphere Gateway上
    * Cloud Foundry内部Component之间的请求
        * 可以通过route直接访问
        * 但需要各个Component的route不能是随机的（虽然developer推到operator时需要审核，route必须为random，但可以审核通过后再改一下，指定route）
        * 还需要在互相调用时有认证信息，比如使用Technical User账号，把jwt格式的认证信息放在http请求的头部