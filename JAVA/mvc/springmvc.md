## Dispatcher Servlet

servlet 具有三个阶段 init-service-destroy

dispatcher servelt 的init 在HttpServletBean中。

```java
/**
	 * Map config parameters onto bean properties of this servlet, and
	 * invoke subclass initialization.
	 * @throws ServletException if bean properties are invalid (or required
	 * properties are missing), or if subclass initialization fails.
	 */	
@Override
	public final void init() throws ServletException {
		// Set bean properties from init parameters.
        //从web.xml获取配置
		PropertyValues pvs = new ServletConfigPropertyValues(getServletConfig(), this.requiredProperties);
		if (!pvs.isEmpty()) {
			try {
                //使用BeanWrapper直接构造Servlet
				BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(this);
				ResourceLoader resourceLoader = new ServletContextResourceLoader(getServletContext());
				bw.registerCustomEditor(Resource.class, new ResourceEditor(resourceLoader, getEnvironment()));
				initBeanWrapper(bw);
				bw.setPropertyValues(pvs, true);
			}
			catch (BeansException ex) {
				throw ex;
			}
		}
		// Let subclasses do whatever initialization they like.
		//是一个protected的方法，用于子类重写。
        initServletBean();
	}
```

在子类的initServletBean中

***protect***方法是用来给子类重写的

初始化了WebApplicationContext。

```java
@Override
protected final void initServletBean() throws ServletException {
    try {
        // 初始化 WebApplicationContext (即SpringMVC的IOC容器)
        this.webApplicationContext = initWebApplicationContext();
        initFrameworkServlet();
    } catch (ServletException ex) {
    } catch (RuntimeException ex) {
    }
}
```

