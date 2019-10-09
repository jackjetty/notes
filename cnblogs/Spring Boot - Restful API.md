* 基本用法
    * @RestController
        * 定义在controller类上
        * Spring4开始引入
        * 表示接口返回的是restful风格的domain object，而不是view
        * 相当于@Controller+@ResponseBody
    * @RequestMapping
        * 可以定义在controller类或者action/method上
        * 可以接受各种Method的接口，只要路径正确
        * 可以通过@RequestMapping(method=GET) 来限定只处理特定的Method请求
        * 通过value指定要处理的路径@RequestMapping(value = { "/xxx" })
    * @GetMapping
        * 定义在action/method上
        * 标识当前接口action/method为Get方式
        * @GetMapping不指定参数时就是指直接使用到controller一级的url就行
        * @GetMapping与参数列表中的@PathVariable（比如url中最后一部分的id参数）、@RequestParam（url中问号后面列出的参数，可以通过defaultValue给定url中没指定参数值时的默认值）描述的参数对应，前者{}中的字符串和后者修饰的变量名对应
        * 可以通过produces指定返回的数据类型，如produces = APPLICATION_JSON_VALUE
    * @PostMapping
        * 定义在action/method上
        * 标识当前接口action/method为Post方式
        * @PostMapping不指定参数时就是指直接使用到controller一级的url就行
        * @PostMapping与参数列表中的@PathVariable？（比如url中最后一部分的id参数）、@RequestParam（url中问号后面列出的参数，可以通过defaultValue给定url中没指定参数值时的默认值）、@RequestBody（仅请求参数是json格式时？？）描述的参数对应，Spring会自动把POST的Request Body部分的JSON转换成方法声明的类。如果转换失败会返回4xx错误，表示请求参数有问题。
    * @PutMapping
        * 可以同时使用@PathVariable和@RequestBody
    * @DeleteMapping
        * 可以同时使用@PathVariable和@RequestParam，还可以使用HttpServletRequest（Spring Boot会自动帮忙传进来）
        * **@RequestParam既可以是url后面?跟的参数，也可以是request form里的key-value参数**
        * @RequestParam有一个required参数，默认为true，如果设为false，就是说这个参数可以不传
    * 返回数据
        * 不需要开发者手动序列化return的对象，Spring Boot使用Jackson自动序列化。Jackson已经自动被加到了classpath中，Spring的MappingJackson2HttpMessageConverter会被自动选来用于转换。
* 响应码
    * @ResponseStatus(OK)指定正常处理并返回请求时的响应码
    * **可以用@ResponseStatus(value = HttpStatus.NOT_FOUND)等来标注自定义的RuntimeException并返回，抛到最外层后就会自动作为该异常类型的响应码返回**
    * @ApiResponses指定当web容器在处理请求并返回400等错误时，可以自定义响应信息。
        * **注意400这种错误并不一定是请求都没到接口上**
        * **这些响应码不是程序返回的，而是web容器判断的，当然应该也可能像.NET那样指定响应请求的响应码**

```
	@RequestMapping(value = "/machineInfo/{assetId}", method = GET, produces = APPLICATION_JSON_VALUE)
	@ApiOperation("Get oee Machine Info.")
	@ResponseStatus(OK)
	@ApiResponses(value = { @ApiResponse(code = SC_OK, message = "", response = MachineInfo.class),
			@ApiResponse(code = SC_BAD_REQUEST, message = Messages.BAD_REQUEST_MESSAGE, response = String.class),
			@ApiResponse(code = SC_UNAUTHORIZED, message = Messages.UNAUTHORIZED_MESSAGE, response = String.class),
			@ApiResponse(code = SC_FORBIDDEN, message = Messages.FORBIDDEN_MESSAGE, response = String.class),
			@ApiResponse(code = SC_NOT_FOUND, message = Messages.NOT_FOUND_MESSAGE, response = String.class) })
	@PreAuthorize("#oauth2.hasScope(this.readRole)")
	public MachineInfo getMachineInfo(@ApiParam(required = true) @PathVariable(required = true) String assetId) {

		LOG.trace("IN/OUT");

		return oeeApiService.getMachineInfo(assetId);
	}
```

* 上传文件
    * 首先要设置consumes为multipart/form-data（上传文件肯定是这个）
        * @PostMapping(value="/files", consumes=MediaType.MULTIPART_FORM_DATA_VALUE)
    * 在方法中写**@RequestParam("file") MultipartFile file**参数，然后在方法中就可以直接使用MultipartFile的流保存文件了，file为调用者上传文件时的request form中指定的名字（name）
        * public Map<String, Object> uploadFile(@RequestParam("file") MultipartFile file)
        * MultipartFile可以直接getInputStream()获取输入流
        * curl可以使用-F参数指定文件上传
* 返回文件、图片等二进制文件/流
    * 首先要设置produces为MediaType.IMAGE_JPEG_VALUE等参数
        * @GetMapping(value="/{id}/icon", produces=MediaType.IMAGE_JPEG_VALUE)
    * 方法返回时返回一个字节数组，注解会自动转换为图片等格式（其实就是设置http response的媒体类型、长度等？？）
* 从请求中获取信息的方法
    * 从URL中获取其路径的一部分
        * 首先需要在RequestMapping做映射，之后在方法中可以通过注解使用映射的变量
            * @GetMapping("/{id}") public TvSeries getOne(@PathVariable("id") Integer id){}
        * 可以写多个
            * @PutMapping("/{id}/characters/{cId}")
        * 还可以使用正则表达式限制类型（不符合要求会返回4xx的错误信息，标识请求参数有问题）
            * @PutMapping("/{id:\\\\d+}")
    * 从POST请求中获取传递来的JSON
        * 给参数前增加**@RequestBody**注解，Spring会自动把POST的Request Body部分的JSON转换成方法声明的类。如果转换失败会返回4xx错误，表示请求参数有问题。
        * public Object updateOne(@RequestBody TvSeries tvSerives)
    * 从文件上传中获取文件流内容
        * 参考前文