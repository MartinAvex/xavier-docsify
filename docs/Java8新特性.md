# Learn

---

- ***CriteriaQuery***
  
    Hibernate Criteria查询（Criteria Query），这种查询方式把查询条件封装为一个Criteria对象。
    
    构建一个Criteria实例，然后把具体的查询条件通过Criteria的add()方法加入到Criteria实例中。
    
    ```java
    //构建查询对象
    CriteriaQuery cq = new CriteriaQuery(ErpHrSalaryBookkeepingEntity.class, dataGrid);
    
    public CriteriaQuery(Class entityClass,DataGrid dg) {
       this.curPage = dg.getPage();
       this.detachedCriteria = DetachedCriteria.forClass(entityClass);
       this.field=dg.getField();  //查询需要显示的字段
       this.entityClass=entityClass; //POJO
       this.dataGrid=dg;
       this.pageSize=dg.getRows();
       this.map= new HashMap<String, Object>();
       this.ordermap = new LinkedHashMap<String, Object>();  //排序字段
    }
    ```
    
    ```java
    //查询条件组装器
    org.jeecgframework.core.extend.hqlsearch.HqlGenerateUtil.installHql(cq, erpHrSalaryBookkeeping, request.getParameterMap());
    
    ```
    

---

- ***Optional***
  
    ```java
    ClassRoom classRoom = null;
    
    //使用map避免NPE
    Long reduce = Optional.ofNullable(classRoom)
    											.map(ClassRoom::getTeacher)
    											.map(Teacher::getStudentList)
    											.orElseGet(ArrayList::new)
    											.stream()
    											.map(Student::getSalary)
    											.reduce(0L, Long::sum);
    
    //NPE
    Long sum = classRoom.getTeacher()
    										.getStudentList()
    										.stream()
    										.map(Student::getSalary)
    										.reduce(0L, Long::sum);
    ```
    
    ![optional](png/optional.png)
    

---

- ***Collectors.collectingAndThen***
  
    先进行结果集的收集，然后将收集到的结果集进行下一步的处理
    
    ```java
    //把第一个参数downstream的结果，交给第二个参数Function函数的参数里面
    public static<T,A,R,RR> Collector<T,A,RR> collectingAndThen(Collector<T,A,R> downstream, Function<R,RR> finisher)
    ```
    
    ```java
    //获取子公司为上海公司，工资最高的员工
    
    Optional<Employee> employee = getAllEmployees().stream()
                    .filter(e -> StringUtils.equals("上海公司", e.getSubCompany()))
                    .max(Comparator.comparingInt(Employee::getSalary));
    
    Employee employee = getAllEmployees().stream()
            .filter(e -> StringUtils.equals("上海公司", e.getSubCompany()))
            .collect(Collectors.collectingAndThen(
    							Collectors.maxBy(Comparator.comparingInt(Employee::getSalary)), Optional::get
    				));
    
    ```