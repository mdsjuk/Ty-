 SpirngBean的生命周期 

 扫描 Bean ------>>BeanDefinition------->BeanFactory(BeanDefinitionMap------->BeanFactoryPostProcess)------>找非懒加载的Bean-------->实例化对象 ------>填充属性 ------> BeanNameAware的setBeanName-----> initializingBean------>单例池